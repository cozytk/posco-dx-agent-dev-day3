# 02. 스토리지 백엔드 & 장기 메모리 & 스킬

## 개요

Deep Agents 프레임워크의 스토리지 백엔드, 장기 메모리, 그리고 스킬(SKILL.md) 시스템을 학습합니다. 에이전트가 파일 시스템을 활용하고, 대화 간 정보를 유지하며, 구조화된 작업 템플릿을 사용하는 방법을 실습합니다.

## 학습 목표

- 5가지 내장 백엔드(State, Filesystem, Store, Composite, LocalShell)의 특성을 이해한다
- CompositeBackend로 경로 기반 라우팅을 구성한다
- BackendProtocol을 구현하여 커스텀 백엔드를 만든다
- StoreBackend를 활용한 크로스 스레드 장기 메모리를 구현한다
- AGENTS.md와 SKILL.md의 차이를 이해하고 활용한다
- Progressive Disclosure 패턴을 이해한다

## 파일 구성

| 파일 | 설명 |
|------|------|
| `00-backends.ipynb` | 스토리지 백엔드 5종 학습 및 커스텀 백엔드 구현 |
| `01-deepagents-skills.ipynb` | 장기 메모리, AGENTS.md, SKILL.md 실습 |
| `02-Github-Copilot-Skill-활용.md` | GitHub Copilot 스킬 활용 가이드 |

## 사전 준비

- `.env` 파일에 `OPENAI_API_KEY` 설정

---

## 실습 1: 스토리지 백엔드 (`00-backends.ipynb`)

### 흐름

1. **환경 설정** - API 키 로드, LLM(`gpt-5.4`) 초기화
2. **백엔드 개요** - 5가지 백엔드 비교 및 추상화 레이어 이해
3. **StateBackend** - 기본 인메모리 백엔드, 스레드 내에서만 유효
4. **FilesystemBackend** - 실제 디스크 접근, `virtual_mode=True`로 안전하게 실습
5. **StoreBackend** - LangGraph Store 기반, 크로스 스레드 영속성
6. **CompositeBackend** - 경로별 라우팅 (`/memories/` → 영속, 나머지 → 임시)
7. **LocalShellBackend** - 셸 실행 기능 (개발 환경 전용, 보안 주의)
8. **커스텀 백엔드** - BackendProtocol 6개 메서드 직접 구현

### 백엔드 비교표

| 백엔드 | 저장소 | 영속성 | 용도 |
|--------|--------|--------|------|
| StateBackend | 메모리 | 스레드 내 | 기본값, 임시 스크래치 |
| FilesystemBackend | 로컬 디스크 | 영구 | 파일 접근, 코딩 에이전트 |
| StoreBackend | LangGraph Store | 크로스 스레드 | 장기 메모리 |
| CompositeBackend | 라우팅 | 혼합 | 메모리 + 임시 파일 |
| LocalShellBackend | 디스크 + 셸 | 영구 | 개발 환경 전용 |

---

## 실습 2: 장기 메모리 & 스킬 (`01-deepagents-skills.ipynb`)

### 흐름

1. **장기 메모리의 필요성** - 대화 간 선호도, 규칙, 피드백 유지
2. **크로스 스레드 메모리** - StoreBackend로 thread-1 ↔ thread-2 간 데이터 공유
3. **AGENTS.md 컨텍스트 주입** - `memory=["/path/AGENTS.md"]`로 항상 로드되는 규칙 설정
4. **SKILL.md 구조** - YAML 프론트매터 + 마크다운 본문, Progressive Disclosure
5. **스킬 실습** - 코드 리뷰 등 구조화된 작업 수행
6. **스킬 소스 우선순위** - base → user → project (Last-wins)
7. **서브에이전트 스킬 상속** - 빌트인은 자동 상속, 커스텀은 명시적 `skills=` 필요

### Memory vs Skills 비교

| 구분 | Memory (AGENTS.md) | Skills (SKILL.md) |
|------|--------------------|--------------------|
| 로딩 | 항상 로드 | 필요 시 온디맨드 |
| 형식 | Markdown | YAML 프론트매터 + Markdown |
| 용도 | 항상 적용할 규칙/규약 | 특정 작업 전용 대형 컨텍스트 |
| 크기 | 소규모 (<1KB) | 대규모 (최대 10MB) |

## 주요 라이브러리

- `deepagents` - create_deep_agent, 각종 백엔드 클래스
- `langchain_openai` - ChatOpenAI
- `langgraph.store.memory` - InMemoryStore
- `langgraph.checkpoint.memory` - MemorySaver
