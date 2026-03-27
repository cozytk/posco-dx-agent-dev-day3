# 01. Agentic RAG — LangGraph로 직접 구축

## 개요

RAG(Retrieval-Augmented Generation)를 3가지 방식으로 구현하며, 단순 검색-응답부터 문서 관련성 평가와 질의 재작성까지 포함하는 고급 RAG 파이프라인을 구축합니다.

## 학습 목표

- RAG의 핵심 5요소(Document Loader, Text Splitter, Embedding, Vector Store, Retriever)를 이해한다
- LangChain Agent 방식, Chain 방식, LangGraph StateGraph 방식의 차이를 비교한다
- 문서 관련성 평가(Grading)와 질의 재작성(Query Rewriting) 패턴을 구현한다
- LangGraph의 조건부 라우팅과 구조화된 출력을 활용한다

## 파일 구성

| 파일 | 설명 |
|------|------|
| `01-langgraph-agentic-rag.ipynb` | RAG 3가지 구현 방식 실습 노트북 |

> 입력 데이터: 프로젝트 루트의 `aibrief-2603.pdf` 파일을 사용합니다.

## 사전 준비

- `.env` 파일에 `OPENAI_API_KEY` 설정
- 프로젝트 루트에 `aibrief-2603.pdf` 파일 존재

## 실습 흐름

1. **환경 설정** - API 키 로드, LLM(`gpt-5.4-mini`) 및 임베딩 모델 초기화
2. **RAG 개념 학습** - 문서 → 청킹 → 임베딩 → 벡터 스토어 파이프라인
3. **문서 로드 & 청킹** - PyPDFLoader로 PDF 로드, RecursiveCharacterTextSplitter로 분할
4. **벡터 스토어 구축** - FAISS 기반 유사도 검색 인덱스 생성
5. **방식 1: LangChain RAG Agent** - `create_agent` + `@tool`로 자율적 검색 판단
6. **방식 2: LangChain RAG Chain** - `@dynamic_prompt` 미들웨어로 단일 LLM 호출
7. **방식 3: LangGraph Custom RAG** - StateGraph 기반 고급 워크플로우
   - `generate_query_or_respond` → 검색 필요 여부 판단
   - `grade_documents` → 문서 관련성 평가 (Pydantic 구조화 출력)
   - `rewrite_question` → 비관련 문서 시 질의 재작성
   - `generate_answer` → 관련 문서 기반 최종 답변 생성

## 3가지 방식 비교

| 방식 | 특징 | 적합한 상황 |
|------|------|-------------|
| **Agent** | LLM이 자율적으로 검색 여부 결정 | 다양한 도구와 함께 유연한 대화 |
| **Chain** | 항상 1회 검색 후 응답 | 단순하고 예측 가능한 QA |
| **LangGraph** | 관련성 평가 + 질의 재작성 포함 | 높은 정확도가 필요한 프로덕션 |

## 주요 라이브러리

- `langgraph` - StateGraph 오케스트레이션
- `langchain_openai` - ChatOpenAI, OpenAIEmbeddings
- `langchain_community` - FAISS 벡터 스토어, PyPDFLoader
- `langchain_text_splitters` - 문서 청킹
