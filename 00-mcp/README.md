# 00. MCP (Model Context Protocol)

## 개요

MCP(Model Context Protocol)의 개념과 아키텍처를 이해하고, LangChain 에이전트에서 MCP 서버를 연동하는 방법을 실습합니다.

## 학습 목표

- MCP의 핵심 구성 요소(Server, Client, Host)를 이해한다
- Stdio / HTTP 두 가지 전송 방식의 차이를 파악한다
- `MultiServerMCPClient`로 여러 MCP 서버를 동시에 연결한다
- MCP 도구를 LangChain 에이전트에 바인딩하여 활용한다

## 파일 구성

| 파일 | 설명 |
|------|------|
| `mcp.ipynb` | MCP 개념 학습 및 실습 노트북 |
| `math_server.py` | Stdio 전송 방식의 수학 연산 MCP 서버 |
| `get_weather.py` | HTTP 전송 방식의 날씨 정보 MCP 서버 |

## 사전 준비

- `.env` 파일에 `OPENAI_API_KEY` 설정
- Weather 서버 사전 실행 필요:
  ```bash
  uv run python get_weather.py
  ```

## 실습 흐름

1. **환경 설정** - API 키 로드, LLM 초기화, 관측성(Observability) 설정
2. **MCP 개념 학습** - Server / Client / Host 아키텍처, Tools / Resources / Prompts 리소스 타입
3. **전송 방식 비교** - Stdio(로컬 프로세스) vs HTTP(원격 서버)
4. **멀티 서버 연동** - Math 서버(Stdio) + Weather 서버(HTTP) 동시 연결
5. **에이전트 테스트** - 수학 연산(`(3+5)×12`) 및 날씨 조회(NYC) 실행

## 주요 라이브러리

- `langchain-mcp-adapters` - MCP ↔ LangChain 연동
- `langchain_openai` - ChatOpenAI LLM
