# 03. 하이브리드 에이전트 — RAG + MCP + Skills

## 개요

앞서 학습한 RAG, MCP, Skills 세 가지 핵심 기술을 하나의 에이전트로 통합하는 하이브리드 에이전트를 `create_deep_agent`로 구축합니다.

## 학습 목표

- RAG 도구, MCP 도구, Skills를 단일 에이전트에 통합한다
- `create_deep_agent`의 통합 아키텍처를 이해한다
- 각 기술이 실제 업무 시나리오에서 어떻게 조합되는지 확인한다
- Langfuse를 활용한 도구 호출 추적 방법을 익힌다

## 파일 구성

| 파일 | 설명 |
|------|------|
| `01-hybrid-agent.ipynb` | 하이브리드 에이전트 통합 실습 노트북 |

> MCP 서버: `../00-mcp/math_server.py`를 서브프로세스로 자동 실행합니다.

## 사전 준비

- `.env` 파일에 `OPENAI_API_KEY` 설정
- Langfuse 서버 실행 중 (`http://localhost:3000`)
  - `LANGFUSE_BASE_URL`, `LANGFUSE_PUBLIC_KEY`, `LANGFUSE_SECRET_KEY` 환경 변수 설정

## 실습 흐름

1. **환경 설정** - API 키 로드, LLM(`gpt-5.4-mini`) 및 임베딩 모델 초기화, Langfuse 설정
2. **RAG 구성** - 회사 규정 문서(연차/출장/경비) 3건을 벡터 스토어에 저장, `retrieve` 도구 정의
3. **MCP 연동** - `math_server.py`를 Stdio로 연결, `add`/`multiply` 도구 자동 탐색
4. **Skills 정의** - 임시 워크스페이스에 주간 보고서 SKILL.md 템플릿 작성
5. **하이브리드 에이전트 생성** - RAG + MCP + Skills를 하나로 통합
6. **3가지 시나리오 테스트**:

### 테스트 시나리오

| 테스트 | 입력 | 사용 도구 | 기대 결과 |
|--------|------|-----------|-----------|
| **RAG** | "우리 회사 연차 규정이 어떻게 되나요? 입사 2년차입니다." | `retrieve` | 연차 규정 안내 (2년차 → 15일) |
| **MCP** | "15 + 27을 계산하고, 그 결과에 3을 곱해줘" | `add`, `multiply` | 15+27=42, 42×3=126 |
| **Skills** | 주간 보고서 작성 요청 (프로젝트 상세 제공) | SKILL.md 템플릿 | 4개 섹션 구조화된 보고서 |

## 핵심 아키텍처

```python
hybrid_agent = create_deep_agent(
    model=model,
    tools=[retrieve, *mcp_tools],    # RAG + MCP 도구 통합
    skills=["/skills/"],             # SKILL.md 자동 탐색
    backend=FilesystemBackend(...),  # 파일 시스템 접근
    system_prompt="...",
)
```

단일 `ainvoke()` 호출로 세 가지 기능 모두 접근 가능하며, Langfuse가 각 요청별 도구 사용을 자동 추적합니다.

## 주요 라이브러리

- `deepagents` - create_deep_agent, FilesystemBackend
- `langchain_openai` - ChatOpenAI, OpenAIEmbeddings
- `langchain_mcp_adapters` - MultiServerMCPClient (Stdio)
- `langchain_core` - InMemoryVectorStore, @tool
- `langfuse` - 관측성 및 도구 호출 추적
