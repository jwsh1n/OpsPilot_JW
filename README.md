# OpsPilot_JW
AI활용 장애 분석
# 🚀 OpsPilot

### LLM 기반 AI 장애 대응 Assistant

운영 로그, 장애 이력, Runbook을 기반으로 **RAG(Retrieval-Augmented Generation)와 LLM을 활용하여 장애 원인과 대응 방법을 제안하는 AI 기반 장애 대응 시스템**

---

## 1. 프로젝트 소개

운영 환경에서 장애가 발생하면 개발자와 운영자는 로그를 확인하고, 과거 장애 이력이나 Runbook을 검색하며 대응 방법을 찾아야 합니다.

하지만 장애 대응에 필요한 정보가 여러 곳에 분산되어 있고, 과거 장애에 대한 경험과 대응 방법이 개인에게 의존하는 문제가 있습니다.

**OpsPilot**은 이러한 장애 대응 과정을 AI로 보조합니다.

장애 정보와 로그를 기반으로 과거 유사 장애와 관련 Runbook을 검색하고, 검색된 정보를 LLM에 제공하여 **장애 원인 후보, 확인 절차, 추천 조치**를 생성합니다.

또한 실제 장애 대응 결과를 기록하여 새로운 장애 대응 지식으로 축적합니다.

---

## 2. 프로젝트 목표

* 장애 대응 과정에서 반복되는 정보 검색 최소화
* 과거 장애 대응 경험의 데이터화
* 장애 상황에 적합한 Runbook 자동 검색
* RAG를 활용한 근거 기반 LLM 응답 생성
* AI 답변에 대한 근거(Source) 제공
* 실제 대응 결과를 새로운 지식으로 축적

---

## 3. 핵심 기능

### 🔍 유사 장애 검색

현재 발생한 장애와 과거 장애 데이터를 Embedding하여 의미적으로 유사한 장애를 검색합니다.

```text
현재 장애
   ↓
Embedding
   ↓
Vector Search
   ↓
유사 장애 Top-K
```

단순 키워드 검색이 아닌 **Semantic Search**를 통해 표현이 달라도 의미가 유사한 장애를 검색하는 것을 목표로 합니다.

---

### 📚 Runbook 검색

장애 상황과 관련된 Runbook 및 운영 문서를 검색합니다.

```text
장애 로그
   ↓
Query 생성
   ↓
Vector Search
   ↓
관련 Runbook / 문서
```

검색된 문서는 이후 LLM의 답변을 구성하는 Context로 사용됩니다.

---

### 🤖 RAG 기반 장애 분석

OpsPilot의 핵심 기능입니다.

현재 장애 정보와 검색된 장애 이력, Runbook을 LLM에 Context로 전달하여 장애를 분석합니다.

```text
                User Input
                    ↓
              Query Embedding
                    ↓
              Vector Retrieval
                    ↓
        ┌───────────┼───────────┐
        ↓           ↓           ↓
      장애 이력    Runbook      문서
        └───────────┼───────────┘
                    ↓
               Context 구성
                    ↓
                   LLM
                    ↓
             장애 분석 결과
```

LLM은 다음 정보를 구조화하여 반환합니다.

```text
원인 후보
확인 절차
추천 조치
주의사항
참고 자료
```

---

### 📖 근거 기반 답변

LLM이 생성한 답변뿐만 아니라 **답변에 사용된 근거 자료를 함께 제공합니다.**

```text
원인 후보
DB Connection Pool 고갈

근거
- 장애 이력 #1023
- Payment API Runbook
- 현재 장애 로그
```

이를 통해 AI가 임의의 정보를 생성하는 것을 줄이고, 사용자가 답변의 근거를 직접 확인할 수 있도록 합니다.

---

### 📝 장애 대응 결과 기록

AI가 추천한 조치를 실제로 수행한 결과를 기록합니다.

```text
AI 분석
   ↓
추천 조치
   ↓
실제 대응
   ↓
대응 결과 기록
   ↓
최종 원인 / 해결 방법
```

축적된 대응 결과는 이후 유사 장애 검색을 위한 데이터로 활용합니다.

---

## 4. AI Pipeline

```text
┌─────────────────┐
│   Incident      │
│   Log / Query   │
└────────┬────────┘
         ↓
┌─────────────────┐
│    Embedding    │
└────────┬────────┘
         ↓
┌─────────────────┐
│  Vector Search  │
│    pgvector     │
└────────┬────────┘
         ↓
┌────────────────────────────┐
│      Context Retrieval     │
│                            │
│  Incident / Runbook / Docs │
└────────────┬───────────────┘
             ↓
┌─────────────────┐
│      Prompt     │
│     Builder     │
└────────┬────────┘
         ↓
┌─────────────────┐
│       LLM       │
└────────┬────────┘
         ↓
┌────────────────────────────┐
│      AI Analysis           │
│                            │
│ Cause / Verification /     │
│ Recommended Action         │
└────────────────────────────┘
```

---

## 5. 주요 기술

### Backend

* Java
* Spring Boot
* Spring Data JPA
* Spring Security
* REST API

### AI / LLM

* OpenAI API
* LangChain4j
* Embedding
* RAG
* Vector Search
* Prompt Engineering
* Structured Output

### Data

* PostgreSQL
* pgvector
* Redis

### Infrastructure

* Docker
* GitHub Actions
* AWS

---

## 6. System Architecture

```text
                       Client
                         │
                         ↓
                  ┌─────────────┐
                  │ Spring Boot │
                  └──────┬──────┘
                         │
             ┌───────────┼───────────┐
             ↓           ↓           ↓
        PostgreSQL     Redis      LLM Service
             │                       │
             ↓                       ↓
          pgvector                OpenAI
             │
             ↓
       RAG Pipeline
             │
       ┌─────┴─────┐
       ↓           ↓
   Retrieval    Embedding
       │
       ↓
  Context Builder
       │
       ↓
      LLM
```

---

## 7. 데이터 구조

주요 데이터는 다음과 같이 관리합니다.

```text
User
 │
 └── Incident
       │
       ├── IncidentLog
       ├── AIAnalysis
       └── ActionHistory

Runbook
 │
 └── Embedding

Document
 │
 └── Embedding
```

### 주요 Entity

| Entity        | 설명           |
| ------------- | ------------ |
| Incident      | 장애 정보        |
| IncidentLog   | 장애 발생 당시 로그  |
| Runbook       | 장애 대응 절차     |
| Document      | 운영 관련 문서     |
| AIAnalysis    | LLM 장애 분석 결과 |
| ActionHistory | 실제 장애 대응 이력  |

---

## 8. 개발 계획

### Phase 1 — Backend 기반 구축

* [ ] 프로젝트 구조 설계
* [ ] API 설계
* [ ] DB 설계
* [ ] 장애 관리 API
* [ ] 로그 관리 API
* [ ] Runbook 관리 API

### Phase 2 — 데이터 및 검색

* [ ] Runbook / 문서 데이터 구성
* [ ] 문서 Chunking
* [ ] Embedding 생성
* [ ] pgvector 저장
* [ ] Semantic Search
* [ ] 유사 장애 검색

### Phase 3 — RAG Pipeline

* [ ] Query Embedding
* [ ] Context Retrieval
* [ ] Prompt 구성
* [ ] LLM 연동
* [ ] RAG Pipeline 구현
* [ ] Structured Output

### Phase 4 — AI 장애 분석

* [ ] 원인 후보 생성
* [ ] 확인 절차 생성
* [ ] 추천 조치 생성
* [ ] 근거 자료 제공
* [ ] 대응 결과 기록

### Phase 5 — RAG / LLM 고도화

* [ ] Query Rewriting
* [ ] Retrieval 개선
* [ ] Reranking
* [ ] Tool Calling
* [ ] Agent 구조 검토

### Phase 6 — 평가 및 개선

* [ ] 장애 테스트 시나리오 구축
* [ ] Retrieval 결과 평가
* [ ] LLM 응답 평가
* [ ] Hallucination 검증
* [ ] Prompt 개선
* [ ] RAG 적용 전/후 비교

### Phase 7 — 배포

* [ ] Docker 구성
* [ ] CI/CD 구축
* [ ] AWS 배포
* [ ] 모니터링 구성

---

## 9. 기대 효과

OpsPilot을 통해 장애 대응에 필요한 정보를 한 곳에서 검색하고, 과거 장애 및 Runbook을 기반으로 AI의 분석 결과를 제공함으로써 장애 대응 과정의 반복적인 검색과 판단 부담을 줄이는 것을 목표로 합니다.

또한 실제 대응 결과를 지속적으로 축적하여 **장애 대응 지식이 데이터로 누적되는 구조**를 구현합니다.

---

## 10. Technical Focus

이 프로젝트에서는 단순한 LLM API 연동을 넘어 다음 기술을 직접 구현하고 검증하는 것을 목표로 합니다.

```text
LLM
 ↓
Prompt Engineering
 ↓
Embedding
 ↓
Vector Search
 ↓
RAG
 ↓
Context Optimization
 ↓
Structured Output
 ↓
LLM Evaluation
```

특히 **검색된 근거를 기반으로 LLM이 답변하도록 설계하고, 검색 품질과 답변 품질을 직접 평가·개선하는 과정**에 중점을 둡니다.
