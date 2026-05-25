# GitHub Spec Kit 활용

> 1강에서 학습한 SDD의 4단계(Specify → Plan → Task → Implement) 워크플로를  
> GitHub Spec Kit 도구로 실제 프로젝트에 어떻게 적용하는지 학습합니다.

---

## 목차

1. [GitHub Spec Kit이란?](#1-github-spec-kit이란)
2. [왜 명세(Specification)가 중요한가?](#2-왜-명세specification가-중요한가)
3. [핵심 개념 이해하기](#3-핵심-개념-이해하기)
4. [프로젝트 디렉터리 구조](#4-프로젝트-디렉터리-구조)
5. [설치 및 초기 설정](#5-설치-및-초기-설정)
6. [GitHub Copilot 연동 방법](#6-github-copilot-연동-방법)
7. [핵심 워크플로 명령어](#7-핵심-워크플로-명령어)
8. [선택적 품질 향상 명령어](#8-선택적-품질-향상-명령어)
   - 8.1 [`/speckit.checklist` — 명세 완성도 점검](#81-speckitchecklist--명세-완성도-점검)
   - 8.2 [`.specify.checklist` 파일이란?](#82-specifychecklist-파일이란)
9. [implement 후 테스트 전략](#9-implement-후-테스트-전략)
   - 9.1 [`/speckit.test` 는 공식 명령어인가?](#91-speckittest-는-공식-명령어인가)
   - 9.2 [방법 1: `/speckit.checklist testing` 활용 (권장)](#92-방법-1-speckitchecklist-testing-활용-권장)
   - 9.3 [방법 2: `/speckit.analyze` 재실행](#93-방법-2-speckitanalyze-재실행)
   - 9.4 [방법 3: `constitution.md` 에 테스트 원칙 명시](#94-방법-3-constitutionmd-에-테스트-원칙-명시-근본적-해결책)
   - 9.5 [세 가지 방법 비교 및 활용 전략](#95-세-가지-방법-비교-및-활용-전략)
10. [전체 개발 워크플로 실습](#10-전체-개발-워크플로-실습)
11. [요구사항이 바뀔 때 대처법](#11-요구사항이-바뀔-때-대처법)
12. [팀 협업 시 활용 팁](#12-팀-협업-시-활용-팁)
13. [자주 묻는 질문 FAQ](#13-자주-묻는-질문-faq)

---

## 1. GitHub Spec Kit이란?

**GitHub Spec Kit**은 소프트웨어 개발의 첫 번째 단계인 **명세(Specification) 작성**을 AI가 도와주는 도구 모음입니다.

> **명세(Specification)란?**  
> "우리가 만들 기능이 어떻게 동작해야 하는지"를 글로 정의한 문서입니다.  
> 건물을 짓기 전에 "어떤 집을 짓고 싶은지" 설계도를 그리는 것과 같습니다.

GitHub Spec Kit은 **Visual Studio Code(VS Code)** 편집기와 **GitHub Copilot** AI를 연결하여 다음을 자동으로 도와줍니다.

- 기능 요구사항 문서 자동 생성
- 누락된 요구사항 발견 및 질문
- 구현 계획 수립
- 작업(Task) 목록 생성
- 아티팩트 간 일관성 검사

### 1강 SDD 방법론과의 연결

1강에서 배운 SDD의 4단계와 GitHub Spec Kit의 명령어는 다음과 같이 정확히 대응됩니다.

| SDD 단계 | 역할 | Spec Kit 명령어 |
|----------|------|----------------|
| 1단계: Specify (지정) | 무엇을 만들지 정의 | `/speckit.specify` + `/speckit.clarify` + `/speckit.checklist` |
| 2단계: Plan (계획) | 어떻게 만들지 설계 | `/speckit.plan` + `/speckit.analyze` |
| 3단계: Task (작업) | 실행 가능한 작업으로 분해 | `/speckit.tasks` |
| 4단계: Implement (구현) | 사양 기반으로 코드 작성 | `/speckit.implement` |
| 검증 단계 | 구현 결과가 명세를 충족하는지 확인 | `/speckit.checklist testing` + `/speckit.analyze` 재실행 |

> **핵심 메시지**  
> GitHub Spec Kit은 SDD 방법론을 실제 프로젝트에서 쉽게 실천할 수 있도록 만든 도구입니다.  

---

## 2. 왜 명세(Specification)가 중요한가?

### 실제 개발 현장의 문제

많은 개발자가 기획을 명확히 하지 않고 바로 코딩을 시작합니다. 이로 인해 다음 문제가 발생합니다.

| 문제 상황 | 결과 |
|---|---|
| 요구사항이 모호한 채로 개발 시작 | 나중에 완전히 다시 만들어야 함 |
| 팀원마다 다르게 이해한 기능 | 합치면 충돌, 오래 걸리는 디버깅 |
| 문서 없이 코드만 존재 | 유지보수가 극도로 어려움 |
| 변경 사항을 코드에만 패치 | 설계 의도가 사라지고 기술 부채 누적 |

### Spec-First(명세 우선) 접근법의 흐름

```
요구사항 정의 → 명세 작성 → 계획 수립 → 작업 분배 → 구현 → 테스트 검증
```

명세를 먼저 작성하면 다음과 같은 효과가 있습니다.

- 코딩 전에 문제를 발견할 수 있다
- 팀원 모두가 같은 이해를 공유한다
- 변경 사항이 체계적으로 전파된다
- 문서와 코드가 함께 유지된다
- 테스트 기준이 명세에서 자동으로 도출된다

### 명세 작성에 드는 시간 대비 효과

```
명세 작성
    │
    └─ 구현 중 요구사항 오해 방지    → 수정 비용 절감 (수 시간)
    └─ 코드 리뷰 시간 단축           → 커뮤니케이션 비용 절감
    └─ 유지보수 시 빠른 파악         → 장기 운영 비용 절감
    └─ 테스트 케이스 자동 도출       → 테스트 설계 비용 절감
```

---

## 3. 핵심 개념 이해하기

### 3.1 아티팩트(Artifact)란?

GitHub Spec Kit에서 생성하는 **문서 파일들**을 아티팩트(Artifact)라고 부릅니다.  
각 아티팩트는 SDD의 특정 단계에 대응됩니다.

| 파일 | SDD 단계 | 역할 | 비유 |
|---|---|---|---|
| `spec.md` | 1단계: Specify | 기능 명세서 (무엇을 만드나) | 설계 요구서 |
| `plan.md` | 2단계: Plan | 구현 계획 (어떻게 만드나) | 시공 계획서 |
| `tasks.md` | 3단계: Task | 작업 목록 (누가 무엇을 언제 하나) | 업무 분장표 |

세 파일은 서로 독립적이지 않고 **순서대로 연결**됩니다.  
`spec.md`가 없으면 `plan.md`를 만들 수 없고, `plan.md`가 없으면 `tasks.md`를 만들 수 없습니다.

### 3.2 constitution.md란?

프로젝트 전체에 적용되는 **기술적 원칙과 제약 조건**을 정의하는 파일입니다.  
모든 기능의 명세와 계획은 이 파일의 원칙을 따라야 합니다.

예시:
```markdown
# 프로젝트 헌법 (constitution.md)

## 기술 스택 제약
- 데이터베이스: Azure SQL Database 사용 (PostgreSQL 사용 금지)
- 인증 방식: OAuth 2.0만 허용 (세션 기반 인증 금지)
- 언어: Python 3.11 이상, Node.js 20 LTS 이상
- 프론트엔드: React + TypeScript (Vue, Angular 사용 금지)

## 품질 기준
- 테스트 커버리지: 80% 이상 유지
- API 응답 시간: 모든 엔드포인트 2초 이내
- 코드 리뷰: 최소 1명 이상의 팀원 승인 필요

## 보안 원칙
- 개인정보는 AES-256으로 암호화 저장
- 비밀번호는 bcrypt 해싱 (평문 저장 절대 금지)
- 모든 API 통신은 HTTPS 필수

## 아키텍처 원칙
- 마이크로서비스 아키텍처 (서비스 간 직접 DB 접근 금지)
- 모든 서비스 간 통신은 REST API 또는 메시지 큐 사용

## 테스트 원칙
- 모든 기능 구현 시 단위 테스트(Unit Test)를 함께 작성한다
- 각 수용 기준(Acceptance Criteria)에 대응하는 테스트 케이스를 반드시 포함한다
```

> **왜 필요한가?**  
> AI가 명세와 계획을 세울 때 이 원칙을 기준으로 삼아 일관성 있는 아키텍처를 유지합니다.  
> constitution.md가 없으면 기능마다 다른 기술 스택이 제안되어 시스템이 파편화됩니다.

> **실습 팁**  
> 팀 프로젝트 시작 전 가장 먼저 팀원 전체가 모여 constitution.md를 함께 작성하세요.  
> 이 파일 하나가 수많은 "그거 왜 그렇게 했어요?" 를 미리 방지합니다.

### 3.3 사용자 스토리(User Story)란?

기능을 **사용자 관점**에서 서술하는 방식입니다.

형식:
> "[역할]로서, 나는 [행동]을 하고 싶다. 왜냐하면 [이유]이기 때문이다."

예시:
> "학생으로서, 나는 과제 파일을 업로드하고 싶다. 왜냐하면 교수님께 제출해야 하기 때문이다."

기술 중심으로 쓰면 안 되는 이유:
```
나쁜 예 (기술 중심):
"파일 업로드 API에 multipart/form-data로 POST 요청을 보내야 한다."

좋은 예 (사용자 중심):
"학생으로서, PDF 파일을 클릭 한 번으로 제출하고 싶다.
왜냐하면 복잡한 절차 없이 빠르게 과제를 제출해야 하기 때문이다."
```

### 3.4 수용 기준(Acceptance Criteria)이란?

"이 기능이 완성되었다"고 판단하는 **구체적인 조건들**입니다.  
Given-When-Then 형식으로 작성합니다.

예시:
```
Given: 학생이 로그인된 상태에서
When:  10MB 이하의 PDF 파일을 업로드하면
Then:  업로드 성공 메시지가 표시되고 파일이 저장된다

Given: 학생이 로그인된 상태에서
When:  10MB를 초과하는 파일을 업로드하려 하면
Then:  "파일 크기 초과" 오류 메시지가 표시된다
```

> **수용 기준과 테스트의 연결**  
> 수용 기준은 단순한 문서가 아닙니다. 구현 후 테스트 케이스의 기준이 됩니다.  
> "Given-When-Then" 하나가 곧 테스트 시나리오 하나입니다.

---

## 4. 프로젝트 디렉터리 구조

GitHub Spec Kit은 다음과 같은 **일관된 폴더 구조**를 사용합니다.

```
my-project/
│
├── .github/                        # GitHub 관련 설정
│   ├── agents/                     # AI 에이전트 설정
│   └── prompts/                    # AI 프롬프트 템플릿 (자동 관리)
│
├── .specify/                       # Spec Kit 내부 설정
│   ├── memory/
│   │   └── constitution.md         # 프로젝트 원칙 및 제약 조건
│   ├── scripts/                    # 자동화 스크립트
│   └── templates/                  # 명세 템플릿
│
├── SourceCode/                     # 실제 구현 코드
│   └── ...
│
└── specs/                          # 모든 명세 아티팩트 모음
    ├── 001-document-upload/        # 기능 001: 문서 업로드
    │   ├── spec.md                 # 기능 명세 (무엇을?)
    │   ├── plan.md                 # 구현 계획 (어떻게?)
    │   ├── tasks.md                # 작업 목록 (언제, 누가?)
    │   └── checklists/             # 도메인별 품질 체크리스트
    │       ├── requirements.md     # /speckit.specify 시 자동 생성
    │       ├── ux.md               # /speckit.checklist ux 실행 결과
    │       ├── security.md         # /speckit.checklist security 실행 결과
    │       └── testing.md          # /speckit.checklist testing 실행 결과 ← 구현 후 검증
    ├── 002-user-authentication/    # 기능 002: 사용자 인증
    │   ├── spec.md
    │   ├── plan.md
    │   ├── tasks.md
    │   └── checklists/
    │       ├── requirements.md
    │       └── security.md
    └── 003-notification-system/    # 기능 003: 알림 시스템
        ├── spec.md
        ├── plan.md
        └── tasks.md
```

### 왜 이런 구조인가?

| 설계 원칙 | 이유 |
|---|---|
| 코드와 명세 분리 | 코드와 문서가 뒤섞이지 않아 관리가 쉬움 |
| 기능별 번호 부여 | 개발 순서와 우선순위를 한눈에 파악 가능 |
| 기능별 독립 폴더 | 여러 팀원이 동시에 작업해도 Git 충돌 없음 |
| specs/ 디렉터리 분리 | 사양이 코드와 동등한 수준의 관리 대상임을 명시 |
| checklists/ 하위 폴더 | 도메인별 품질 검사 결과를 기능과 함께 버전 관리 |

---

## 5. 설치 및 초기 설정

### 사전 준비물

- [ ] VS Code 설치
- [ ] GitHub 계정
- [ ] GitHub Copilot 구독 (학생은 GitHub Education으로 무료 사용 가능)
- [ ] GitHub Spec Kit 확장 프로그램 설치

> **GitHub Education 무료 혜택**  
> 재학 중인 학생은 [https://education.github.com](https://education.github.com) 에서  
> 학교 이메일로 신청하면 GitHub Copilot을 무료로 사용할 수 있습니다.

### 초기화 명령어

VS Code 터미널에서 프로젝트 루트 디렉터리로 이동한 후 다음 명령어를 실행합니다.

```bash
# GitHub Copilot 연동으로 초기화
specify init my-project --ai copilot
```

이 명령어를 실행하면 다음이 자동으로 처리됩니다.

1. `.github/prompts/` 디렉터리가 자동 생성됩니다
2. AI 프롬프트 템플릿이 자동으로 설치됩니다
3. VS Code가 `/speckit.*` 명령을 인식하도록 설정됩니다
4. `specs/` 디렉터리와 기본 구조가 생성됩니다

> **주의**: 초기화는 프로젝트 루트 디렉터리에서 실행해야 합니다.

### GitHub Codespaces 환경에서 실습하는 경우

수업 실습 환경으로 GitHub Codespaces를 사용하는 경우, 별도 설치 없이 브라우저에서 바로 실습할 수 있습니다.

```
1. GitHub 저장소에서 [Code] → [Codespaces] → [Create codespace on main] 클릭
2. Codespaces가 열리면 VS Code 웹 환경이 자동으로 구동됩니다
3. 터미널에서 specify init 명령어를 실행합니다
4. 좌측 Extensions 탭에서 GitHub Copilot 확장이 설치되어 있는지 확인합니다
```

---

## 6. GitHub Copilot 연동 방법

초기화 후, VS Code에서 **GitHub Copilot 채팅** 창을 열고 `/speckit.` 명령어를 입력하면 AI가 즉시 반응합니다.

```
1. VS Code 실행
2. Ctrl+Shift+I (Mac: Cmd+Shift+I) 또는 View > GitHub Copilot Chat으로 채팅 창 열기
3. 채팅 창에 /speckit.specify 입력
4. 기능 설명 입력 → AI가 명세 생성
```

### AI가 자동으로 처리하는 것 vs 직접 해야 하는 것

| 자동 처리 (AI가 알아서) | 직접 입력 (개발자가 해야 함) |
|---|---|
| 프롬프트 템플릿 로딩 | 기능 설명 제공 |
| constitution.md 참조 삽입 | AI의 명확화 질문에 답변 |
| 명세 형식(Given/When/Then) 관리 | 생성된 명세 검토 및 수정 |
| 아티팩트 파일 저장 | 팀원과 명세 내용 합의 |

---

## 7. 핵심 워크플로 명령어

### 전체 워크플로 순서

```
/speckit.specify  →  /speckit.clarify  →  /speckit.checklist  →  /speckit.plan  →  /speckit.analyze  →  /speckit.tasks  →  /speckit.implement  →  [테스트 검증]
      ①                    ②                      ③                    ④                   ⑤                    ⑥                    ⑦                      ⑧
  명세 생성           간격 분석            품질 체크리스트          계획 수립           일관성 검사          작업 생성            코드 구현              구현 결과 검증
  [SDD 1단계]        [SDD 1단계]         [SDD 1단계]            [SDD 2단계]         [SDD 2단계]         [SDD 3단계]         [SDD 4단계]         [검증 단계 → 9절 참조]
```

> ③ `/speckit.checklist`에서 미완성 항목이 발견되면, spec.md 보완 후 재실행합니다.  
> ⑤ `/speckit.analyze`에서 불일치가 발견되면, 수정 후 다시 ⑤를 실행하여 모두 해결된 뒤 ⑥으로 넘어갑니다.  
> ⑧ implement 후 테스트 검증 방법은 **9절**에서 상세히 다룹니다.

---

### ① `/speckit.specify` — 기능 명세 생성

**역할**: 기능 설명을 입력하면 완전한 명세 문서(spec.md)를 자동 생성합니다.

**생성되는 내용**:

- 사용자 스토리 (User Stories)
- 수용 기준 (Acceptance Criteria)
- 기능 요구사항 (Functional Requirements)
- 비기능 요구사항 (Non-Functional Requirements)
- 엣지 케이스 (Edge Cases)

**사용 예시**:

```
[사용자 입력]
/speckit.specify
과제 제출 기능을 만들려고 합니다. 학생이 PDF 파일을 업로드하고
교수가 확인할 수 있어야 합니다.

[AI 출력 — spec.md 생성]

## 사용자 스토리
- 학생으로서, PDF 파일(최대 10MB)을 업로드하고 싶다.
  왜냐하면 교수님께 과제를 제출해야 하기 때문이다.
- 교수로서, 제출된 과제 목록을 확인하고 싶다.
  왜냐하면 학생 제출 현황을 파악해야 하기 때문이다.

## 수용 기준
Given: 학생이 로그인 상태일 때
When:  PDF 파일을 선택하고 제출 버튼을 클릭하면
Then:  업로드 완료 메시지와 제출 시각이 표시된다
...
```

---

### ② `/speckit.clarify` — 명세 간격 분석 및 명확화

**역할**: 명세의 모호한 부분, 누락된 세부사항, 미처리된 엣지 케이스를 찾아 질문합니다.

**AI가 생성하는 질문 예시**:

```
[AI 출력]
명세를 분석한 결과 다음 항목이 명확하지 않습니다.

Q1. 파일 업로드 시 최대 동시 업로드 수는 몇 개인가요?
    a) 1개 (순차 처리)
    b) 최대 5개 동시 업로드
    c) 제한 없음

Q2. 네트워크 오류로 업로드가 중단될 경우:
    a) 처음부터 다시 업로드
    b) 이어받기(Resume) 지원
    c) 오류 메시지만 표시

Q3. 이미 같은 파일명이 존재할 때:
    a) 덮어쓰기
    b) 파일명에 번호 추가 (과제_1.pdf, 과제_2.pdf)
    c) 오류 메시지 표시
```

> **이 단계가 중요한 이유**  
> 숙련된 개발 분석가가 명세를 검토하는 것과 같습니다.  
> 구현 도중에 이런 질문이 나오면, 이미 작성한 코드를 수정해야 하는 상황이 됩니다.  
> 명세 단계에서 1시간 투자 = 구현 단계에서 10시간 절약

---

### ③ `/speckit.plan` — 구현 계획 수립

**역할**: spec.md를 바탕으로 기술적 구현 계획(plan.md)을 생성합니다.  
이때 constitution.md에 정의된 기술 스택 원칙을 자동으로 참조합니다.

**생성되는 내용**:

- 아키텍처 설계
- 데이터베이스 스키마
- API 엔드포인트 설계
- 기술 스택 선택 이유
- 단계별 구현 순서

**예시 출력 (plan.md 일부)**:

```markdown
# 구현 계획: 과제 제출 기능 (plan.md)

## 아키텍처
- Frontend: React + TypeScript
- Backend:  FastAPI (Python)
- Database: Azure SQL Database  ← constitution.md 원칙 반영
- Storage:  Azure Blob Storage  ← 파일 저장소

## API 설계
POST /api/assignments/upload   - 과제 파일 업로드
GET  /api/assignments          - 과제 목록 조회 (교수용)
GET  /api/assignments/{id}     - 특정 과제 상세 조회

## 구현 순서
1. Azure Blob Storage 컨테이너 설정
2. DB 스키마 생성 (assignments 테이블)
3. 업로드 API 구현 및 유효성 검사
4. React 업로드 UI 컴포넌트 개발
5. 교수용 목록 조회 페이지 개발
```

---

### ④ `/speckit.analyze` — 아티팩트 일관성 검사

**역할**: spec.md, plan.md, tasks.md, constitution.md 간의 **불일치를 자동으로 발견**합니다.  
모든 문서가 서로 일치하는지 검증하는 품질 게이트(Quality Gate) 역할을 합니다.

**발견 가능한 불일치 예시**:

```
[AI 분석 결과]

⚠ [불일치 1] 데이터베이스
   plan.md는 PostgreSQL 사용을 제안하지만,
   constitution.md는 Azure SQL Database를 요구합니다.
   → plan.md 수정 필요

⚠ [불일치 2] 감사 로그
   spec.md는 모든 파일 접근에 감사 로그를 요구하지만,
   plan.md에 로깅 구현 계획이 없습니다.
   → plan.md에 로깅 섹션 추가 필요

⚠ [불일치 3] 누락된 작업
   plan.md는 데이터베이스 마이그레이션을 언급하지만,
   tasks.md에 해당 작업이 없습니다.
   → tasks.md에 마이그레이션 작업 추가 필요
```

불일치를 모두 수정한 후 `/speckit.analyze`를 재실행합니다.

```
[재실행 결과]
✅ spec.md ↔ plan.md: 모든 요구사항 대응 확인
✅ plan.md ↔ tasks.md: 모든 계획 항목 작업화 확인
✅ plan.md ↔ constitution.md: 기술 스택 원칙 준수 확인
→ 다음 단계(/speckit.tasks)로 진행 가능합니다.
```

---

### ⑤ `/speckit.tasks` — 작업 목록 생성

**역할**: 구현 계획을 **개발자(또는 AI 에이전트)가 바로 실행할 수 있는 구체적인 작업 목록**으로 변환합니다.

**예시 출력 (tasks.md)**:

```markdown
# 작업 목록: 과제 제출 기능 (tasks.md)

## Sprint 1 — 기반 설정 (2일)
- [ ] Azure Blob Storage 컨테이너 생성 및 접근 권한 설정
- [ ] Azure SQL Database assignments 테이블 스키마 생성
- [ ] FastAPI 프로젝트 초기 설정 및 의존성 설치

## Sprint 2 — 핵심 기능 구현 (3일)
- [ ] 파일 업로드 API 구현 (POST /api/assignments/upload)
- [ ] 파일 크기(10MB) 및 형식(PDF) 유효성 검사 로직
- [ ] 감사 로그 미들웨어 구현
- [ ] 과제 목록 조회 API 구현 (GET /api/assignments)
- [ ] React 파일 업로드 UI 컴포넌트 개발

## Sprint 3 — 테스트 및 마무리 (1일)
- [ ] 단위 테스트 작성 (커버리지 80% 이상)
- [ ] 통합 테스트 작성 (업로드 → 목록 조회 시나리오)
- [ ] API 문서 자동 생성 확인 (FastAPI Swagger)
```

---

### ⑥ `/speckit.implement` — 사양 기반 코드 구현

**역할**: tasks.md의 작업 목록을 하나씩 실제 코드로 구현하도록 AI가 지원합니다.  
단순히 코드를 생성하는 것이 아니라, **spec.md의 수용 기준을 코드에 그대로 반영**합니다.

**사용 방법**:

```
[사용자 입력]
/speckit.implement
Sprint 1의 첫 번째 작업(Azure Blob Storage 설정)부터 시작해 주세요.
```

**구현 사이클**:

```
작업 선택 → spec.md 수용 기준 확인 → 코드 생성 → 수용 기준과 대조 검증 → 다음 작업
                                                           │
                                          (미충족 시 spec.md 재검토 후 코드 수정)
```

**AI 지원 예시 (FastAPI 로그인 API)**:

```python
# Implements: User Story - 학생 과제 제출
# Acceptance Criteria: AC-001-1, AC-001-2 (파일 크기 및 형식 검증)
# Task: TASK-002 (파일 업로드 API 구현)

from fastapi import APIRouter, UploadFile, File, HTTPException
from typing import Annotated

router = APIRouter()

MAX_FILE_SIZE = 10 * 1024 * 1024  # 10MB
ALLOWED_TYPES = {"application/pdf"}

@router.post("/api/assignments/upload")
async def upload_assignment(
    file: Annotated[UploadFile, File(description="제출할 PDF 파일")]
):
    # AC-001-2: PDF 형식만 허용
    if file.content_type not in ALLOWED_TYPES:
        raise HTTPException(status_code=400, detail="PDF 파일만 업로드 가능합니다.")

    # AC-001-1: 10MB 이하만 허용
    contents = await file.read()
    if len(contents) > MAX_FILE_SIZE:
        raise HTTPException(status_code=400, detail="파일 크기는 10MB 이하여야 합니다.")

    # TODO: Azure Blob Storage에 저장 로직
    saved_url = await save_to_blob_storage(file.filename, contents)

    return {
        "message": "업로드가 완료되었습니다.",
        "filename": file.filename,
        "submitted_at": datetime.utcnow().isoformat()
    }
```

> **핵심 포인트**  
> 코드 주석에 `Implements`, `Acceptance Criteria`, `Task` 참조 정보가 포함됩니다.  
> 이것이 1강에서 배운 **추적성(Traceability)** 입니다.  
> 나중에 버그가 생겼을 때 "이 코드는 어떤 요구사항에서 나왔는가"를 즉시 추적할 수 있습니다.

---

## 8. 선택적 품질 향상 명령어

### 8.1 `/speckit.checklist` — 명세 완성도 점검

명세의 완성도를 특정 **도메인(영역) 관점**에서 검증하는 체크리스트를 자동 생성합니다.  
"요구사항의 단위 테스트(Unit Tests for English)"라고 부를 만큼, 명세서가 구현에 들어가기 전에 얼마나 잘 쓰였는지를 객관적으로 평가합니다.

> **`/speckit.analyze`와의 차이**
>
> | 명령어 | 목적 | 비유 |
> |---|---|---|
> | `/speckit.checklist` | 하나의 명세를 도메인 관점에서 품질 점검 | 문서 작성 품질 검사 |
> | `/speckit.analyze` | 여러 문서 간 불일치 탐지 | 문서들 간 교차 검증 |

---

#### 지원 도메인(Domain) 종류

`/speckit.checklist` 뒤에 도메인 키워드를 붙여 원하는 관점으로 검사합니다.

| 도메인 키워드 | 파일명 | 주요 검사 내용 |
|---|---|---|
| `ux` | `checklists/ux.md` | 사용자 흐름, 오류 메시지, 빈 상태, 모바일 반응형 등 |
| `security` | `checklists/security.md` | 인증·인가, 입력값 검증, 암호화, 데이터 보호 등 |
| `testing` | `checklists/testing.md` | 테스트 시나리오 커버리지, 엣지 케이스, 경계값 등 |
| `performance` | `checklists/performance.md` | 응답 시간, 부하 처리, 캐싱, 페이지네이션 등 |
| `accessibility` | `checklists/accessibility.md` | WCAG 기준, 키보드 탐색, 스크린 리더 지원 등 |
| `api` | `checklists/api.md` | 엔드포인트 정의, 에러 코드, 버전 관리, rate limit 등 |
| _(키워드 없음)_ | `checklists/requirements.md` | 전반적인 요구사항 완성도 (자동 생성) |

---

#### 사용 방법

```
/speckit.checklist ux          # UX 관점 체크리스트 생성
/speckit.checklist security    # 보안 관점 체크리스트 생성
/speckit.checklist testing     # 테스트 시나리오 체크리스트 생성
/speckit.checklist performance # 성능 요구사항 체크리스트 생성
/speckit.checklist api         # API 설계 체크리스트 생성
```

> **실행 시점 권장**
> - `/speckit.clarify` 이후, `/speckit.plan` **이전**에 실행 → 설계 전 명세 완성도 확인
> - `/speckit.implement` **이후**에 `testing` 도메인으로 실행 → 구현 결과 검증 ← **9절에서 상세 설명**

---

#### 도메인별 생성 예시

---

##### ① `/speckit.checklist ux` — UX 체크리스트

```
[사용자 입력]
/speckit.checklist ux
```

**AI가 생성하는 `checklists/ux.md`**:

```markdown
# UX 체크리스트: 로그인 화면
# 생성일: 2025-01-15 | 기준 문서: spec.md

## 사용자 흐름 (User Flow)
- [ ] CHK001 - 로그인 성공 후 이동 경로(/dashboard)가 명확히 정의되어 있는가? [spec.md §기능요구사항]
- [ ] CHK002 - 로그인 실패 후 사용자가 다시 시도할 수 있는 흐름이 명시되어 있는가? [spec.md §수용기준]
- [ ] CHK003 - 이미 로그인된 상태에서 로그인 페이지 접근 시 처리 방법이 정의되어 있는가? [spec.md §엣지케이스]

## 오류 처리 및 메시지 (Error Handling)
- [ ] CHK004 - 모든 입력 오류(빈 값, 형식 불일치, 서버 오류)에 구체적인 메시지가 정의되어 있는가? [spec.md §수용기준]
- [ ] CHK005 - 오류 메시지가 사용자 친화적 언어로 작성되어 있는가? ("오류 코드 401" (X) → "비밀번호가 틀렸습니다" (O))
- [ ] CHK006 - 네트워크 오류 발생 시 사용자에게 표시되는 메시지가 정의되어 있는가? [spec.md §엣지케이스]
- [ ] CHK007 - 계정 잠금 상태에서의 안내 메시지가 명시되어 있는가? [spec.md §엣지케이스]

## 빈 상태 (Empty State)
- [ ] CHK008 - 초기 화면(아무것도 입력하지 않은 상태)의 동작이 정의되어 있는가?
- [ ] CHK009 - 로그인 버튼 비활성화 조건이 명시되어 있는가? (예: 필드가 비어있을 때)

## 반응형 및 접근성 (Responsive & Accessibility)
- [ ] CHK010 - 모바일(375px 이상) 화면 지원이 명시되어 있는가? [spec.md §비기능요구사항]
- [ ] CHK011 - 비밀번호 표시/숨김 토글 기능이 명세에 포함되어 있는가? [spec.md §기능요구사항]
- [ ] CHK012 - 키보드만으로 폼을 완성하고 제출할 수 있어야 함이 정의되어 있는가?

## 피드백 (Feedback)
- [ ] CHK013 - 로그인 처리 중(로딩) 상태의 UI 표현이 명시되어 있는가? (예: 버튼 스피너)
- [ ] CHK014 - 로그인 성공 후 사용자에게 주는 피드백이 정의되어 있는가? (예: 환영 메시지)

---
총 14개 항목 | "아니오" 항목은 spec.md에 보완 후 다음 단계로 진행하세요.
```

---

##### ② `/speckit.checklist security` — 보안 체크리스트

```
[사용자 입력]
/speckit.checklist security
```

**AI가 생성하는 `checklists/security.md`**:

```markdown
# 보안 체크리스트: 로그인 화면
# 생성일: 2025-01-15 | 기준 문서: spec.md, constitution.md

## 인증 (Authentication)
- [ ] CHK001 - 비밀번호가 HTTPS로만 전송됨이 명시되어 있는가? [spec.md §비기능요구사항]
- [ ] CHK002 - 비밀번호가 서버 측에서 bcrypt 등으로 해싱됨이 명시되어 있는가? [constitution.md §보안원칙]
- [ ] CHK003 - JWT 토큰 만료 시간이 정의되어 있는가? (accessToken / refreshToken 구분) [spec.md]
- [ ] CHK004 - "로그인 상태 유지" 기능의 토큰 저장 방식이 명시되어 있는가? (localStorage vs Cookie)

## 브루트 포스 방지 (Brute Force Prevention)
- [ ] CHK005 - 로그인 실패 횟수 제한이 정의되어 있는가? (예: 5회) [spec.md §비기능요구사항]
- [ ] CHK006 - 계정 잠금 기간 및 자동 해제 방식이 명시되어 있는가? [spec.md §엣지케이스]
- [ ] CHK007 - 계정 잠금 시 사용자 알림(이메일 등) 발송 여부가 정의되어 있는가?

## 입력값 검증 (Input Validation)
- [ ] CHK008 - 이메일 형식 유효성 검사가 클라이언트와 서버 양쪽에 명시되어 있는가?
- [ ] CHK009 - SQL Injection, XSS 등 입력값 공격 방어 요구사항이 명시되어 있는가?
- [ ] CHK010 - 비밀번호 최소 복잡도 요구사항이 정의되어 있는가? (길이, 특수문자 등)

## 세션 관리 (Session Management)
- [ ] CHK011 - 로그아웃 시 서버 측 토큰 무효화 처리가 명시되어 있는가?
- [ ] CHK012 - 동시 세션 처리 정책이 정의되어 있는가? (다중 기기 로그인 허용 여부)

---
총 12개 항목 | "아니오" 항목은 spec.md 또는 constitution.md에 보완 후 다음 단계로 진행하세요.
```

---

##### ③ `/speckit.checklist testing` — 테스트 체크리스트

```
[사용자 입력]
/speckit.checklist testing
```

**AI가 생성하는 `checklists/testing.md`**:

```markdown
# 테스트 체크리스트: 로그인 화면
# 생성일: 2025-01-15 | 기준 문서: spec.md

## 단위 테스트 시나리오 (Unit Test Scenarios)
- [ ] CHK001 - 올바른 이메일/비밀번호로 로그인 성공 시나리오가 명세에 있는가? [AC-login-1]
- [ ] CHK002 - 잘못된 비밀번호 입력 시 오류 응답 시나리오가 명세에 있는가? [AC-login-2]
- [ ] CHK003 - 존재하지 않는 이메일 입력 시 처리 시나리오가 명세에 있는가?
- [ ] CHK004 - 빈 입력값으로 제출 시 처리 시나리오가 명세에 있는가?
- [ ] CHK005 - 이메일 형식 오류(예: "abc@") 입력 시 시나리오가 명세에 있는가?

## 경계값 테스트 (Boundary Testing)
- [ ] CHK006 - 로그인 실패 4회(잠금 직전)와 5회(잠금) 케이스가 명세에 있는가? [spec.md §엣지케이스]
- [ ] CHK007 - 계정 잠금 상태에서 로그인 시도 시 처리 시나리오가 있는가?
- [ ] CHK008 - 잠금 해제 후 첫 번째 로그인 성공 시나리오가 있는가?

## 엣지 케이스 (Edge Cases)
- [ ] CHK009 - 이미 로그인된 상태에서 로그인 페이지 접근 처리가 명세에 있는가?
- [ ] CHK010 - 네트워크 단절 중 로그인 시도 시나리오가 있는가?
- [ ] CHK011 - JWT 토큰 만료 직후 API 요청 처리 시나리오가 있는가?

## 통합 테스트 (Integration Test)
- [ ] CHK012 - 로그인 성공 → 대시보드 이동 흐름 전체가 명세에 커버되는가?
- [ ] CHK013 - 모바일(375px) 환경에서의 UI 렌더링 검증이 명세에 언급되어 있는가?

---
총 13개 항목 | 모든 항목이 "예"여야 tasks.md에 테스트 작업이 빠짐없이 반영됩니다.
```

---

#### 체크리스트 결과 활용 방법

```
1. 체크리스트 실행
        ↓
2. "아니오" 항목 발견
        ↓
3. spec.md 해당 항목 보완
        ↓
4. 체크리스트 재실행 (/speckit.checklist ux 재입력)
        ↓
5. 모든 항목 "예" 확인
        ↓
6. 다음 단계(/speckit.plan 또는 implement 후 검증)로 진행
```

> 한 기능에 체크리스트를 여러 도메인으로 실행해도 됩니다.  
> 예: 로그인 기능 → `/speckit.checklist ux` 후 `/speckit.checklist security` 순서로 실행  
> 각 결과는 `checklists/ux.md`, `checklists/security.md`에 별도 저장됩니다.

---

### 8.2 `.specify.checklist` 파일이란?

`/speckit.checklist` 명령 실행 결과는 **`specs/NNN-기능명/checklists/`** 폴더 안에 저장됩니다.  
이 파일들을 통칭하여 **.specify.checklist** 파일이라고 부릅니다.

#### 파일 구조

```
specs/001-login/
│
├── spec.md                     # 기능 명세
├── plan.md                     # 구현 계획
├── tasks.md                    # 작업 목록
│
└── checklists/                 # ← .specify.checklist 파일 모음
    ├── requirements.md         # /speckit.specify 실행 시 자동 생성 (기본 완성도 점검)
    ├── ux.md                   # /speckit.checklist ux 결과 (implement 전)
    ├── security.md             # /speckit.checklist security 결과 (implement 전)
    ├── testing.md              # /speckit.checklist testing 결과 (implement 전·후 모두 활용)
    └── performance.md          # /speckit.checklist performance 결과
```

#### `requirements.md` — 자동 생성 기본 체크리스트

`/speckit.specify` 실행 시 spec.md와 함께 자동으로 생성되는 기본 완성도 체크리스트입니다.

```markdown
# 요구사항 완성도 체크리스트: 로그인 화면 (requirements.md)
# /speckit.specify 자동 생성 | 수동 편집 가능

## 요구사항 완전성 (Requirement Completeness)
- [ ] [NEEDS CLARIFICATION] 마커가 남아있는 항목이 없는가?
- [ ] 모든 사용자 스토리에 대응하는 수용 기준(AC)이 존재하는가?
- [ ] 모든 수용 기준이 Given-When-Then 형식으로 작성되어 있는가?
- [ ] 비기능 요구사항에 측정 가능한 수치가 포함되어 있는가?
  - 예: "빠른 응답" (X) → "응답 시간 2초 이내" (O)
- [ ] 모든 외부 의존성(외부 API, 라이브러리)이 명시되어 있는가?

## 명확성 (Clarity)
- [ ] 모호한 단어("적절히", "빠르게", "쉽게" 등)가 없는가?
- [ ] 역할(Role)별 기능 제한이 명확히 구분되어 있는가?
- [ ] 서로 모순되는 요구사항이 없는가?

## 보안 및 데이터 (Security & Data)
- [ ] 보안 요구사항이 명시되어 있는가? (인증, 권한, 암호화 등)
- [ ] 개인정보 처리 방침이 정의되어 있는가?
- [ ] 데이터 보존 및 삭제 정책이 정의되어 있는가?

## 기능 준비도 (Feature Readiness)
- [ ] 이 명세만 보고 plan.md를 작성할 수 있는가?
- [ ] 팀 전원이 동일하게 이해할 수 있는가?
- [ ] constitution.md의 원칙을 위반하는 항목이 없는가?

---
모든 항목이 체크되면 /speckit.plan 진행 가능합니다.
```

#### 체크리스트 파일 관리 규칙

| 규칙 | 내용 |
|---|---|
| 버전 관리 | Git에 함께 커밋하여 검토 이력 보관 |
| 항목 ID(CHK001...) | 기존 파일에 항목 추가 시 마지막 번호 이어서 작성 |
| 기존 내용 보존 | 재실행 시 기존 항목 삭제 금지, 새 항목만 추가 |
| PR 첨부 | Pull Request 시 해당 기능의 checklist 파일도 함께 포함 |

---

## 9. implement 후 테스트 전략

### 9.1 `/speckit.test` 는 공식 명령어인가?

> **결론: GitHub Spec Kit 공식 명령어에 `/speckit.test` 는 현재 존재하지 않습니다.**

GitHub Spec Kit이 제공하는 공식 명령어는 아래 7개가 전부입니다.

| 명령어 | 역할 |
|---|---|
| `/speckit.constitution` | 프로젝트 원칙 설정 |
| `/speckit.specify` | 요구사항 정의 |
| `/speckit.clarify` | 모호함 제거 |
| `/speckit.checklist` | 명세 완성도 점검 |
| `/speckit.plan` | 기술 설계 |
| `/speckit.tasks` | 작업 목록 생성 |
| `/speckit.analyze` | 문서 간 일관성 점검 |
| `/speckit.implement` | 코드 생성 |

공식 Quick Start 문서는 implement 이후의 검증 수단으로 `/speckit.analyze` 재실행과 `/speckit.checklist testing` 을 제시하고 있습니다.

> **왜 별도 테스트 명령어가 없는가?**  
> Spec Kit의 설계 철학은 **"테스트 기준은 명세(spec.md)의 수용 기준에서 이미 정의된다"** 는 것입니다.  
> 즉, Given-When-Then 형식의 수용 기준이 곧 테스트 시나리오이며,  
> `constitution.md`에 테스트 원칙을 선언해 두면 implement 시 테스트 코드가 함께 생성됩니다.  
>
> GitHub Spec Kit Issues #1745에서 커뮤니티가 `/speckit.verify` 추가를 요청 중이며,  
> Issues #1323에서는 `/speckit.review` 로 최종 품질 게이트를 만들자는 제안도 있습니다.  
> 두 이슈 모두 아직 미반영 상태입니다(2026년 5월 기준).

---

### 9.2 방법 1: `/speckit.checklist testing` 활용 (권장)

implement 완료 후 가장 직접적인 방법입니다.  
`spec.md`의 수용 기준이 실제 코드에 반영되어 있는지를 **테스트 시나리오 관점**에서 점검합니다.

#### 실행 방법

```
[사용자 입력]
/speckit.checklist testing
생성된 코드가 spec.md 의 모든 수용 기준을 충족하는지
테스트 시나리오 관점에서 점검해줘.
누락되거나 구현되지 않은 기능이 있으면 알려줘.
```

#### implement 이전 vs 이후 실행의 차이

| 구분 | implement 이전 (`/speckit.plan` 전) | implement 이후 (코드 생성 후) |
|---|---|---|
| 목적 | spec.md 에 테스트 시나리오가 충분히 정의되어 있는가? | 코드가 spec.md 의 수용 기준을 실제로 충족하는가? |
| 점검 대상 | 명세 문서의 완성도 | 생성된 코드와 명세의 일치 여부 |
| 결과 | 누락된 수용 기준 → spec.md 보완 | 미구현 기능 → 코드 수정 요청 |

#### AI 출력 예시 (implement 후 실행)

```markdown
# 테스트 검증 결과: 로그인 화면 (implement 후)

## 수용 기준 대비 구현 상태

### ✅ 구현 완료
- AC-login-1: admin/1234 로그인 성공 → 대시보드 이동 ✅
- AC-login-2: 잘못된 비밀번호 → 오류 메시지 표시 ✅
- AC-login-3: 빈 입력 → "이메일과 비밀번호를 입력하세요" 표시 ✅
- AC-login-4: 이메일 형식 오류 → "올바른 이메일 형식" 메시지 ✅

### ❌ 미구현 / 불일치 발견
- AC-login-5: 5회 실패 → 계정 잠금
  현재 코드: 실패 횟수 카운트 없음
  → 수정 필요

- AC-login-6: 로그인 처리 중 버튼 비활성화
  현재 코드: 중복 클릭 방지 로직 없음
  → 수정 필요

### 권장 조치
1. Redis 기반 실패 횟수 카운터 구현 추가
2. 로그인 버튼에 로딩 상태 및 disabled 처리 추가
```

#### 미구현 항목 발견 시 처리

```
[미구현 항목 발견 시]

1. Gemini / Copilot Chat 에 수정 요청
   예) "AC-login-5 — 로그인 5회 실패 시 계정 잠금 로직을 login.py 에 추가해줘"

2. 코드 수정 후 브라우저에서 직접 확인

3. /speckit.checklist testing 재실행 → 전체 통과 확인

4. Git 커밋
   예) "fix: 로그인 5회 실패 잠금 로직 추가 (AC-login-5)"
```

---

### 9.3 방법 2: `/speckit.analyze` 재실행

공식 Quick Start 문서는 implement 이후에도 `/speckit.analyze`를 추가 검토 목적으로 반복 실행할 수 있다고 명시합니다.

**implement 이후 analyze 의 역할**: spec.md 에 정의된 수용 기준이 코드에 반영되지 않아 발생하는 **명세-코드 드리프트(Spec Drift)** 를 탐지합니다.

```
[사용자 입력]
/speckit.analyze
implement 이후 spec.md 의 수용 기준이 코드에 모두 반영되었는지 확인해줘.
명세에는 있지만 코드에 반영되지 않은 항목이 있으면 알려줘.
```

**예상 출력**:

```
[분석 결과]

✅ spec.md ↔ plan.md: 모든 요구사항 대응 확인
✅ plan.md ↔ tasks.md: 모든 계획 항목 작업화 확인

⚠ [명세-코드 드리프트 감지]
   spec.md AC-login-5: "5회 실패 시 계정 잠금" 수용 기준 존재
   코드: 실패 횟수 카운터 로직 없음
   → 구현 누락 가능성 — 코드 확인 및 보완 필요
```

> **방법 1과 방법 2의 차이**  
> 방법 1(`/speckit.checklist testing`)은 **테스트 시나리오 관점**에서 "어떤 케이스가 빠졌는가"를 찾습니다.  
> 방법 2(`/speckit.analyze`)는 **문서 간 일관성 관점**에서 "명세와 코드가 어긋난 곳이 있는가"를 찾습니다.  
> 중요한 기능이라면 두 방법을 모두 실행하는 것이 가장 안전합니다.

---

### 9.4 방법 3: `constitution.md` 에 테스트 원칙 명시 (근본적 해결책)

방법 1과 2는 **구현 후 사후 검증**이지만, 방법 3은 **구현 중에 테스트 코드가 자동 생성**되도록 하는 근본적인 해결책입니다.

프로젝트 시작 시 `constitution.md`에 테스트 원칙을 선언하면, `/speckit.implement` 실행 시 AI가 테스트 코드를 기능 코드와 함께 자동으로 작성합니다.

#### constitution.md 에 테스트 원칙 추가

```
[사용자 입력]
/speckit.constitution
기존 원칙에 아래 테스트 원칙을 추가해줘:

## 테스트 원칙
- 모든 기능 구현 시 단위 테스트(Unit Test)를 함께 작성한다
- 테스트 커버리지는 80% 이상을 유지한다
- 각 수용 기준(Acceptance Criteria)에 대응하는 테스트 케이스를 반드시 포함한다
- 테스트 파일명은 구현 파일명에 _test 또는 .test를 붙여 작성한다
  예: login.py → test_login.py / login.ts → login.test.ts
- E2E 테스트는 주요 사용자 시나리오(로그인 성공, 실패, 잠금)를 모두 커버한다
```

#### constitution.md 적용 후 implement 결과 변화

constitution.md에 테스트 원칙이 있으면, `/speckit.implement` 시 AI가 기능 코드와 테스트 코드를 함께 생성합니다.

```python
# [기능 코드] login.py
# Implements: AC-login-1, AC-login-2, AC-login-3
# Task: TASK-002

@router.post("/api/auth/login")
def login(req: LoginRequest):
    ...
```

```python
# [테스트 코드] test_login.py  ← constitution.md 원칙에 따라 자동 생성
# Test Cases: AC-login-1 (성공), AC-login-2 (실패), AC-login-3 (잠금)

import pytest

class TestLogin:
    def test_login_success(self, client):
        """AC-login-1: 올바른 이메일/비밀번호로 로그인 성공"""
        response = client.post("/api/auth/login", json={
            "email": "user@example.com",
            "password": "correct_password"
        })
        assert response.status_code == 200
        assert "accessToken" in response.json()

    def test_login_wrong_password(self, client):
        """AC-login-2: 잘못된 비밀번호 → 401 오류"""
        response = client.post("/api/auth/login", json={
            "email": "user@example.com",
            "password": "wrong_password"
        })
        assert response.status_code == 401
        assert "올바르지 않습니다" in response.json()["detail"]

    def test_login_account_locked(self, client, redis_mock):
        """AC-login-3: 5회 실패 → 423 잠금"""
        redis_mock.set("login_fail:user@example.com", 5)
        response = client.post("/api/auth/login", json={
            "email": "user@example.com",
            "password": "any_password"
        })
        assert response.status_code == 423
```

> **이 방법이 가장 강력한 이유**  
> 수동으로 테스트를 요청하지 않아도 됩니다. constitution.md 에 원칙만 선언하면  
> 모든 기능의 implement 단계에서 테스트 코드가 자동으로 함께 생성됩니다.  
> SDD의 핵심인 "명세가 진실의 근원" 원칙이 테스트 코드에도 그대로 적용됩니다.

---

### 9.5 세 가지 방법 비교 및 활용 전략

| 구분 | 방법 1: `/speckit.checklist testing` | 방법 2: `/speckit.analyze` 재실행 | 방법 3: `constitution.md` 테스트 원칙 |
|---|---|---|---|
| 실행 시점 | implement 완료 후 | implement 완료 후 | 프로젝트 시작 시 (1회) |
| 주요 역할 | 수용 기준 충족 여부 시나리오 점검 | 명세-코드 드리프트 탐지 | implement 시 테스트 코드 자동 생성 |
| 결과물 | `checklists/testing.md` 업데이트 | 드리프트 리포트 | 기능 코드 + 테스트 코드 동시 생성 |
| 난이도 | 쉬움 (명령 한 줄) | 쉬움 (명령 한 줄) | 보통 (constitution.md 작성 필요) |
| 추천 대상 | 개별 기능 완료 후 빠른 점검 | 변경 사항 반영 후 일관성 확인 | 모든 프로젝트 (특히 팀 프로젝트) |

#### 상황별 권장 조합

```
[빠른 프로토타입]
implement → /speckit.checklist testing (결과 확인 후 필요 시 수정)

[일반 팀 프로젝트]
constitution.md 테스트 원칙 선언 (1회)
→ implement (테스트 코드 자동 생성)
→ /speckit.checklist testing (누락 항목 확인)
→ /speckit.analyze (명세-코드 드리프트 점검)

[보안·규정 준수가 중요한 프로젝트]
constitution.md 테스트 원칙 선언
→ implement 전: /speckit.checklist testing (테스트 시나리오 명세 점검)
→ implement (테스트 코드 자동 생성)
→ implement 후: /speckit.checklist testing 재실행 (구현 결과 검증)
→ /speckit.analyze (최종 일관성 점검)
```

#### implement 후 전체 테스트 검증 흐름 요약

```
/speckit.implement 완료
        │
        ├── 방법 3 적용 시 → 테스트 코드 이미 생성됨
        │
        ├── /speckit.checklist testing
        │     ↓
        │   미구현 항목 발견
        │     ↓
        │   코드 수정 요청 → 재실행 → 전체 통과
        │
        ├── /speckit.analyze (재실행)
        │     ↓
        │   명세-코드 드리프트 탐지
        │     ↓
        │   수정 후 재실행 → 전체 통과
        │
        └── Git 커밋 (코드 + 테스트 + 체크리스트 함께)
```

---

## 10. 전체 개발 워크플로 실습

### 예제: "로그인 화면 만들기"

아래는 수업 실습에서 활용할 수 있는 전체 흐름입니다.  
가장 기본적인 기능인 로그인 화면을 통해 GitHub Spec Kit의 전 과정을 경험합니다.

---

#### Step 1: 명세 생성 — `/speckit.specify`

```
[사용자 입력]
/speckit.specify
이메일과 비밀번호로 로그인할 수 있는 화면이 필요합니다.
로그인 실패 시 오류 메시지를 보여주고,
성공 시 대시보드 페이지로 이동해야 합니다.
```

**AI가 생성하는 spec.md**:

```markdown
# 기능 명세: 로그인 화면 (spec.md)

## 사용자 스토리
- 사용자로서, 이메일과 비밀번호를 입력하여 서비스에 로그인하고 싶다.
  왜냐하면 내 개인화된 대시보드에 접근해야 하기 때문이다.
- 사용자로서, 로그인 실패 시 무엇이 잘못되었는지 알고 싶다.
  왜냐하면 올바른 정보를 다시 입력할 수 있어야 하기 때문이다.

## 기능 요구사항
- 이메일 입력 필드 (텍스트)
- 비밀번호 입력 필드 (마스킹 처리)
- 로그인 버튼
- 로그인 성공 시 → 대시보드 페이지(/dashboard)로 이동
- 로그인 실패 시 → 오류 메시지 표시 (페이지 이동 없음)

## 수용 기준 (Acceptance Criteria)

Given: 사용자가 로그인 페이지에 있을 때
When:  올바른 이메일과 비밀번호를 입력하고 로그인 버튼을 클릭하면
Then:  대시보드 페이지로 이동하고 환영 메시지가 표시된다

Given: 사용자가 로그인 페이지에 있을 때
When:  잘못된 비밀번호를 입력하고 로그인 버튼을 클릭하면
Then:  "이메일 또는 비밀번호가 올바르지 않습니다" 메시지가 표시된다

Given: 사용자가 로그인 페이지에 있을 때
When:  이메일 형식이 아닌 값을 입력하면
Then:  "올바른 이메일 형식을 입력해 주세요" 안내 메시지가 표시된다

Given: 사용자가 로그인 페이지에 있을 때
When:  아무것도 입력하지 않고 로그인 버튼을 클릭하면
Then:  "이메일과 비밀번호를 입력해 주세요" 메시지가 표시된다

## 비기능 요구사항
- 로그인 API 응답 시간: 2초 이내
- 비밀번호는 평문으로 전송 금지 (HTTPS 필수)
- 로그인 실패 5회 이상 시 계정 잠금 (보안)
- 모바일 화면(375px 이상)에서도 정상 표시

## 엣지 케이스
- 비밀번호 5회 연속 실패 → 계정 잠금 안내 및 이메일 발송
- 이미 로그인된 상태에서 로그인 페이지 접근 → 대시보드로 자동 리다이렉트
- 네트워크 오류 발생 시 → "서버에 연결할 수 없습니다. 잠시 후 다시 시도해 주세요" 표시
```

---

#### Step 2: 명확화 질문 답변 — `/speckit.clarify`

```
[사용자 입력]
/speckit.clarify
```

**AI가 발견한 모호한 부분과 질문 예시**:

```
[AI 출력]
명세를 분석한 결과, 다음 항목이 명확하지 않습니다.

Q1. "비밀번호 찾기" 또는 "아이디 찾기" 링크가 필요한가요?
    a) 로그인 화면에 포함
    b) 이번 기능 범위에서 제외 (추후 개발)
    c) 미정

Q2. "로그인 상태 유지" (자동 로그인) 체크박스가 필요한가요?
    a) 필요함 (세션 유지 기간: ___일)
    b) 불필요함

Q3. 소셜 로그인(Google, Kakao 등) 버튼을 포함해야 하나요?
    a) Google 로그인 포함
    b) Kakao 로그인 포함
    c) 이번 범위에서는 이메일/비밀번호만

Q4. 계정 잠금 해제 방법은 무엇인가요?
    a) 일정 시간(예: 30분) 경과 후 자동 해제
    b) 이메일 인증 후 해제
    c) 관리자가 수동으로 해제
```

**팀의 답변 적용 결과**:

```
Q1 → b) 비밀번호 찾기 링크 포함, 아이디 찾기 제외
Q2 → a) 로그인 상태 유지 7일
Q3 → c) 이메일/비밀번호만 (소셜 로그인은 002 기능으로 분리)
Q4 → a) 30분 후 자동 해제 + 이메일 알림
```

> **이 단계의 핵심**  
> Q2의 "로그인 상태 유지"는 처음 명세에 없었지만 AI가 발굴했습니다.  
> 구현 중에 이 질문이 나왔다면 이미 절반 완성된 코드를 수정해야 했을 것입니다.

---

#### Step 2.5: 명세 품질 점검 — `/speckit.checklist`

```
[사용자 입력]
/speckit.checklist ux
```

**AI가 생성하는 `checklists/ux.md` (일부)**:

```markdown
# UX 체크리스트: 로그인 화면

## 사용자 흐름
- [x] CHK001 - 로그인 성공 후 /dashboard 이동 경로가 명확히 정의되어 있는가? ✅
- [x] CHK002 - 실패 후 재시도 흐름이 명시되어 있는가? ✅
- [x] CHK003 - 이미 로그인된 상태 접근 시 처리가 정의되어 있는가? ✅

## 오류 메시지
- [x] CHK004 - 모든 입력 오류에 구체적인 메시지가 정의되어 있는가? ✅
- [ ] CHK005 - 로딩 중 버튼 비활성화 또는 스피너 표시가 명세에 있는가? ❌ → 보완 필요

## 반응형
- [x] CHK006 - 모바일(375px 이상) 지원이 명시되어 있는가? ✅
- [ ] CHK007 - 태블릿(768px) 지원 여부가 정의되어 있는가? ❌ → 보완 필요
```

**❌ 항목 발견 시 spec.md 보완**:

```markdown
# spec.md 추가 내용

## 기능 요구사항 (보완)
- 로그인 버튼: API 호출 중 로딩 스피너 표시 및 중복 클릭 방지
- 반응형: 모바일(375px 이상), 태블릿(768px 이상) 지원
```

이후 `/speckit.checklist ux` 재실행 → 전체 통과 확인 후 다음 단계로 진행합니다.

```
[재실행 결과]
✅ 모든 UX 항목 통과. /speckit.plan 진행 가능합니다.
```

---

#### Step 3: 구현 계획 수립 — `/speckit.plan`

```
[사용자 입력]
/speckit.plan
```

**AI가 생성하는 plan.md**:

```markdown
# 구현 계획: 로그인 화면 (plan.md)

## 기술 스택
- Frontend: React + TypeScript
- 상태 관리: React useState (간단한 폼 상태)
- HTTP 클라이언트: Axios
- Backend API: FastAPI (Python)
- 인증 방식: JWT (Access Token + Refresh Token)
- 데이터베이스: Azure SQL Database (users 테이블)  ← constitution.md 반영

## 화면 구성

  +----------------------------------+
  |         서비스 로고              |
  |                                  |
  |  [이메일 입력 필드          ]    |
  |  [비밀번호 입력 필드  👁]       |
  |  □ 로그인 상태 유지 (7일)       |
  |                                  |
  |  [      로그인 버튼      ]       |
  |                                  |
  |  비밀번호 찾기                   |
  +----------------------------------+

## API 설계
  POST /api/auth/login
    요청:  { email: string, password: string, rememberMe: boolean }
    성공(200): { accessToken, refreshToken, user: { id, name } }
    실패(401): { message: "이메일 또는 비밀번호가 올바르지 않습니다" }
    잠금(423): { message: "계정이 잠겼습니다. 30분 후 다시 시도하세요" }

## 보안 처리
- HTTPS 전송 필수
- 비밀번호 bcrypt 해싱 (서버 측)
- 로그인 실패 횟수 Redis에 저장 (TTL: 30분)
- JWT 만료시간: accessToken 1시간, refreshToken 7일

## 구현 순서
1. DB users 테이블 스키마 확인 및 마이그레이션
2. 로그인 API 엔드포인트 구현
3. 실패 횟수 카운터 로직 구현 (Redis)
4. React 로그인 폼 컴포넌트 구현
5. 유효성 검사 로직 구현 (프론트엔드)
6. API 연동 및 토큰 저장
7. 리다이렉트 처리
```

---

#### Step 4: 일관성 검사 — `/speckit.analyze`

```
[사용자 입력]
/speckit.analyze
```

**AI 분석 결과**:

```
[AI 출력]
✅ 사용자 스토리 ↔ 수용 기준: 모두 대응됨

⚠ [불일치 1] 계정 잠금 해제
   spec.md: "30분 후 자동 해제 + 이메일 알림"
   plan.md: Redis TTL로 자동 해제만 언급, 이메일 알림 로직 없음
   → plan.md에 이메일 발송 서비스 연동 계획 추가 필요

⚠ [불일치 2] 비밀번호 필드 마스킹 토글
   spec.md: 비밀번호 마스킹 처리 명시
   plan.md 화면 구성: 👁 아이콘 표시됨
   tasks.md: 해당 UI 구현 작업 항목 없음
   → tasks.md에 "비밀번호 표시/숨김 토글 버튼 구현" 작업 추가 필요

⚠ [불일치 3] 모바일 반응형
   spec.md: 375px 이상 지원 명시
   plan.md: 반응형 CSS 처리 계획 없음
   → plan.md에 CSS 미디어 쿼리 또는 Tailwind 반응형 처리 명시 필요
```

**수정 후 재검사**:

```
[사용자 입력]
/speckit.analyze

[AI 출력]
✅ 모든 항목 일치 확인됨. 다음 단계(/speckit.tasks) 진행 가능합니다.
```

---

#### Step 5: 작업 목록 생성 — `/speckit.tasks`

```
[사용자 입력]
/speckit.tasks
```

**AI가 생성하는 tasks.md**:

```markdown
# 작업 목록: 로그인 화면 (tasks.md)

## Sprint 1 — 백엔드 기반 구축 (2일)
- [ ] users 테이블 스키마 확인 및 DB 마이그레이션 스크립트 작성
- [ ] POST /api/auth/login 엔드포인트 구현
- [ ] bcrypt 비밀번호 검증 로직 구현
- [ ] JWT 발급 및 검증 유틸 구현
- [ ] Redis 연동 — 로그인 실패 횟수 카운터 구현 (TTL 30분)
- [ ] 계정 잠금 시 이메일 알림 발송 연동

## Sprint 2 — 프론트엔드 구현 (2일)
- [ ] LoginForm 컴포넌트 마크업 및 스타일링
- [ ] 이메일 형식 유효성 검사 구현
- [ ] 비밀번호 표시/숨김 토글 버튼 구현 (👁 아이콘)
- [ ] "로그인 상태 유지" 체크박스 구현 (7일 토큰 저장)
- [ ] 로그인 API 연동 (Axios)
- [ ] 성공 시 /dashboard 리다이렉트 처리
- [ ] 실패 시 오류 메시지 표시 처리
- [ ] 계정 잠금 상태 안내 메시지 처리
- [ ] 모바일 반응형 CSS 적용 (375px 이상)

## Sprint 3 — 테스트 및 마무리 (1일)
- [ ] 단위 테스트: 로그인 API (성공/실패/잠금 케이스)
- [ ] 단위 테스트: 유효성 검사 함수
- [ ] E2E 테스트: 로그인 성공 → 대시보드 이동
- [ ] E2E 테스트: 5회 실패 → 잠금 메시지 확인
- [ ] 보안 점검: HTTPS 설정, 토큰 만료 확인
```

---

#### Step 6: 구현 시작 — `/speckit.implement`

```
[사용자 입력]
/speckit.implement
Sprint 1의 첫 번째 작업부터 시작해 주세요.
```

**AI 지원 예시 — 로그인 API (FastAPI)**:

```python
# Implements: User Story - 이메일/비밀번호 로그인
# Acceptance Criteria: AC-login-1 (성공), AC-login-2 (실패), AC-login-3 (잠금)
# Task: TASK-002 (POST /api/auth/login 엔드포인트 구현)

from fastapi import APIRouter, HTTPException
from pydantic import BaseModel, EmailStr
import bcrypt
import redis

router = APIRouter()
r = redis.Redis()

class LoginRequest(BaseModel):
    email: EmailStr
    password: str
    rememberMe: bool = False

@router.post("/api/auth/login")
def login(req: LoginRequest):
    fail_key = f"login_fail:{req.email}"
    fail_count = int(r.get(fail_key) or 0)

    # AC-login-3: 5회 실패 시 계정 잠금
    if fail_count >= 5:
        raise HTTPException(423, "계정이 잠겼습니다. 30분 후 다시 시도하세요.")

    user = get_user_by_email(req.email)  # DB 조회 함수

    # AC-login-2: 이메일 또는 비밀번호 불일치
    if not user or not bcrypt.checkpw(req.password.encode(), user.password_hash):
        r.incr(fail_key)
        r.expire(fail_key, 1800)  # 30분 TTL
        raise HTTPException(401, "이메일 또는 비밀번호가 올바르지 않습니다.")

    # AC-login-1: 로그인 성공
    r.delete(fail_key)  # 실패 횟수 초기화
    token = create_jwt(user.id, days=7 if req.rememberMe else 1)
    return {
        "accessToken": token,
        "user": {"id": user.id, "name": user.name}
    }
```

> **코드를 보면서 확인할 것**  
> spec.md에서 정의한 모든 수용 기준(성공, 실패, 잠금, 이메일 알림)이  
> 주석과 코드에 그대로 반영되어 있는지 확인하세요.  
> 이것이 SDD에서 말하는 "사양과 코드의 1:1 추적성"입니다.

---

#### Step 7: 구현 후 테스트 검증 ← NEW

implement 완료 후, **9절**에서 배운 방법으로 검증을 진행합니다.

**7-1. `/speckit.checklist testing` 으로 수용 기준 충족 점검**

```
[사용자 입력]
/speckit.checklist testing
implement 완료 후 spec.md 의 모든 수용 기준이 코드에 반영되었는지
테스트 시나리오 관점에서 점검해줘.
```

```markdown
[AI 출력 — checklists/testing.md 업데이트]

## 수용 기준 구현 상태

✅ AC-login-1: 로그인 성공 → 대시보드 이동 구현됨
✅ AC-login-2: 잘못된 비밀번호 → 401 오류 구현됨
✅ AC-login-3: 5회 실패 → 423 잠금 구현됨
✅ AC-login-4: 빈 입력 → 검증 메시지 구현됨

❌ AC-login-5: 로딩 중 버튼 비활성화
   현재 코드에서 로딩 스피너 및 disabled 처리 없음
   → 프론트엔드 LoginForm 컴포넌트 수정 필요

→ AC-login-5 수정 후 재실행하세요.
```

**7-2. `/speckit.analyze` 재실행으로 명세-코드 드리프트 점검**

```
[사용자 입력]
/speckit.analyze

[AI 출력]
✅ spec.md ↔ 코드: 수용 기준 반영 확인
✅ plan.md ↔ 코드: 구현 계획 일치 확인
→ 모든 점검 통과. PR 제출 가능합니다.
```

**7-3. Git 커밋 — 코드 + 테스트 + 체크리스트 함께**

```bash
git add specs/001-login/checklists/testing.md  # 테스트 검증 결과
git add src/api/login.py                        # 기능 코드
git add tests/test_login.py                     # 테스트 코드
git commit -m "feat(001): 로그인 API 구현 + 테스트 검증 완료

- AC-login-1~5 수용 기준 모두 충족
- /speckit.checklist testing 전체 통과
- /speckit.analyze 명세-코드 일관성 확인"
```

---

## 11. 요구사항이 바뀔 때 대처법

실제 개발에서 요구사항은 항상 변합니다.  
GitHub Spec Kit은 변경 사항이 **체계적으로 전파**되도록 설계되어 있습니다.

### 변경 대처 순서

```
1. spec.md 수정      (무엇이 변경되었는가? → 사양이 진실의 근원)
        ↓
2. /speckit.clarify  (변경된 명세에 새로운 모호함이 없는지 확인)
        ↓
3. /speckit.checklist (변경된 명세에 새로운 구멍이 없는지 점검)
        ↓
4. /speckit.plan 재실행   (변경된 명세 기반으로 계획 재수립)
        ↓
5. /speckit.tasks 재실행  (변경된 계획 기반으로 작업 목록 재생성)
        ↓
6. /speckit.analyze       (새로운 불일치 없는지 최종 확인)
        ↓
7. 구현 코드 업데이트
        ↓
8. /speckit.checklist testing (변경 후 수용 기준 충족 여부 재검증)
```

> **왜 spec.md부터 수정하는가?**  
> SDD의 핵심 원칙은 "사양이 진실의 근원"입니다.  
> 코드를 먼저 수정하면 사양과 코드가 어긋나는 순간부터 문서는 거짓 문서가 됩니다.

### 나쁜 방법 vs 좋은 방법

| 나쁜 방법 | 좋은 방법 |
|---|---|
| 코드만 바꾸고 문서는 그대로 둠 | spec.md부터 순서대로 업데이트 |
| Slack 메시지로만 변경 공유 | 아티팩트에 공식 반영 후 공유 |
| 기억에 의존해서 변경 사항 추적 | Git 커밋으로 변경 이력 관리 |
| 변경 영향 범위를 직관으로 파악 | `/speckit.analyze`로 자동 탐지 |
| 변경 후 테스트 검증 생략 | `/speckit.checklist testing` 재실행 |

### 변경 요구사항 예시

```
[상황] 출시 3일 전, 기획자가 요청했습니다.
"로그인 실패 잠금 기준을 5회에서 3회로 바꿔주세요."

[나쁜 방법]
개발자가 코드에서 숫자 5를 3으로만 바꿈
→ spec.md에는 여전히 "5회"로 적혀 있음
→ checklists/testing.md 의 CHK006 도 여전히 "5회 기준"
→ 6개월 후 신규 팀원이 spec.md를 보고 혼란

[좋은 방법]
1. spec.md의 "5회" → "3회"로 수정
2. /speckit.analyze 실행 → plan.md와 tasks.md 자동 불일치 탐지
3. 영향받는 부분만 수정
4. /speckit.checklist testing 재실행 → 변경된 기준으로 테스트 검증
5. Git 커밋: "feat: 계정 잠금 기준 5회→3회 변경 (spec/plan/code/test 모두 반영)"
```

---

## 12. 팀 협업 시 활용 팁

### 기능 병렬 개발

```
팀원 A: specs/001-login/ 작업
팀원 B: specs/002-dashboard/ 작업    ← Git 충돌 없음!
팀원 C: specs/003-notification/ 작업
```

각 기능이 독립된 폴더를 가지므로 **Git 충돌(conflict) 없이 병렬 작업**이 가능합니다.

### 코드 리뷰 시 활용

PR(Pull Request)을 올릴 때 명세 아티팩트도 함께 포함하면 다음 효과가 있습니다.

- 리뷰어가 "왜 이렇게 구현했는가"를 바로 이해
- 명세와 코드 일치 여부를 쉽게 검토 가능
- 불필요한 질문과 커뮤니케이션 비용 감소

**PR 설명 예시**:
```
## 변경 사항
- 로그인 화면 구현 (specs/001-login/ 참조)

## 명세 연결
- spec.md: 모든 수용 기준 달성 확인
- /speckit.analyze: 불일치 없음 확인 (스크린샷 첨부)
- /speckit.checklist testing: 전체 통과 확인 (checklists/testing.md 참조)

## 테스트
- 단위 테스트 커버리지: 85%
- E2E 테스트: 로그인 성공/실패/잠금 시나리오 모두 통과
```

### constitution.md 공동 관리

팀 전체가 합의한 기술 원칙을 constitution.md에 문서화합니다.  
변경 시 반드시 팀 전체 동의를 거치도록 규칙을 정합니다.

```
constitution.md 변경 프로세스 예시:

1. 변경 제안 → GitHub Issue 등록
2. 팀 미팅에서 찬반 논의
3. 합의 후 PR로 변경
4. 팀 전원 리뷰 승인 후 머지
5. 기존 기능의 plan.md에 영향 있으면 /speckit.analyze로 확인
```

---

## 13. 자주 묻는 질문 FAQ

**Q: GitHub Copilot 없이 사용할 수 있나요?**  
A: 현재 이 가이드는 Copilot 연동 기준입니다. GitHub Copilot 대신 Claude, Gemini 등 다른 AI 도구를 사용하는 경우, 각 도구의 공식 Spec Kit 연동 문서를 참고하세요. 명령어 체계는 동일하고 AI 모델만 교체됩니다.

**Q: implement 후 테스트를 위한 전용 명령어가 있나요?**  
A: `/speckit.test` 는 2026년 5월 현재 공식 명령어에 없습니다. 커뮤니티에서 추가를 요청 중이며(Issues #1745, #1323), 현재는 세 가지 방법으로 대응합니다. 첫째, `/speckit.checklist testing` 을 implement 후 실행하여 수용 기준 충족 여부를 점검합니다. 둘째, `/speckit.analyze` 를 재실행하여 명세-코드 드리프트를 탐지합니다. 셋째, `constitution.md` 에 테스트 원칙을 선언하여 implement 시 테스트 코드가 자동 생성되도록 합니다. 자세한 내용은 **9절**을 참고하세요.

**Q: `/speckit.checklist testing` 을 implement 전에도 실행해야 하나요?**  
A: 목적이 다릅니다. implement **전**에 실행하면 spec.md 에 테스트 시나리오가 충분히 정의되어 있는지를 점검합니다. implement **후**에 실행하면 코드가 spec.md 의 수용 기준을 실제로 충족하는지를 검증합니다. 중요한 기능이라면 두 번 모두 실행하는 것이 좋습니다.

**Q: `/speckit.checklist`는 반드시 실행해야 하나요?**  
A: 선택 사항이지만, 팀 프로젝트나 보안·UX가 중요한 기능이라면 강력히 권장합니다. 특히 로그인, 결제, 개인정보 처리 같은 민감한 기능에서 `/speckit.checklist security`를 실행하면 놓치기 쉬운 보안 요구사항을 사전에 발견할 수 있습니다. 실행 비용(시간)은 수 분이지만, 구현 후 발견했을 때의 수정 비용은 수 시간이 될 수 있습니다.

**Q: 체크리스트 도메인을 여러 개 실행해도 되나요?**  
A: 네, 동일한 기능에 여러 도메인 체크리스트를 실행할 수 있습니다. 각 결과는 별도 파일(ux.md, security.md 등)로 저장되며 서로 덮어쓰지 않습니다. 중요 기능은 `ux` + `security` + `testing` 세 가지를 모두 실행하는 것을 추천합니다.

**Q: 체크리스트에서 "아니오"가 많이 나왔습니다. 처음부터 다시 해야 하나요?**  
A: 아닙니다. "아니오" 항목에 해당하는 내용만 spec.md에 보완하고, 체크리스트를 재실행하면 됩니다. 이 반복 과정이 바로 명세를 점진적으로 완성해 가는 Spec-First 개발의 핵심입니다.

**Q: constitution.md 에 테스트 원칙을 추가하면 어떤 효과가 있나요?**  
A: `/speckit.implement` 실행 시 AI가 기능 코드와 테스트 코드를 함께 생성합니다. 즉, 매번 `/speckit.checklist testing` 을 수동으로 실행하지 않아도 테스트 코드가 자동으로 포함됩니다. 프로젝트 시작 시 constitution.md 에 한 번만 선언하면 이후 모든 기능에 자동 적용되므로, 팀 프로젝트에서 특히 효과적입니다.

**Q: GitHub Codespaces에서 실습해도 되나요?**  
A: 네, Codespaces는 VS Code 환경을 브라우저에서 그대로 제공하므로 별도 설치 없이 실습할 수 있어 수업 실습에 매우 적합합니다. GitHub Education 계정이 있으면 Codespaces도 일정 시간 무료로 사용할 수 있습니다.

**Q: 명세를 영어로 작성해야 하나요?**  
A: 한국어로 작성해도 됩니다. AI가 언어에 관계없이 처리합니다. 단, 팀 내 언어를 통일할 것을 권장합니다. 여러 언어가 섞이면 `/speckit.analyze` 검색 정확도가 낮아질 수 있습니다.

**Q: 작은 프로젝트에도 이 도구가 필요한가요?**  
A: 기능이 2~3개 이상이거나 팀 프로젝트라면 적극 권장합니다. 개인 1인 프로젝트라도 향후 포트폴리오로 공개할 경우, 명세 아티팩트가 있으면 프로젝트의 완성도와 신뢰성이 크게 높아집니다.

**Q: AI가 생성한 명세를 그대로 사용해도 되나요?**  
A: AI의 출력은 **초안(Draft)**으로 보고, 반드시 팀원과 검토 후 사용하세요. AI는 도메인 지식(업무 특성, 조직 규칙 등)을 완벽히 알 수 없으므로 검토가 필수입니다. "AI가 만들었으니 맞겠지"는 가장 위험한 태도입니다.

**Q: spec.md, plan.md, tasks.md는 직접 수정해도 되나요?**  
A: 당연히 됩니다. AI가 생성한 파일은 출발점입니다. 팀 상황에 맞게 자유롭게 편집하세요. 다만 수정 후에는 반드시 `/speckit.analyze`를 실행하여 다른 아티팩트와의 일관성을 확인하세요.

**Q: constitution.md는 언제 만들어야 하나요?**  
A: 프로젝트 시작 직후, 첫 번째 기능 명세(`/speckit.specify`)를 실행하기 **전에** 만드세요. constitution.md가 없으면 AI가 기능마다 다른 기술 스택을 제안할 수 있습니다.

---

## 핵심 정리

### 명령어 요약

| 명령어 | SDD 단계 | 역할 |
|---|---|---|
| `/speckit.specify` | 1단계: Specify | 기능 설명 → spec.md 자동 생성 |
| `/speckit.clarify` | 1단계: Specify | 모호한 요구사항을 AI가 질문으로 발굴 |
| `/speckit.checklist [도메인]` | 1단계: Specify / 검증 | 도메인별 명세 완성도 점검 + 구현 후 검증 |
| `/speckit.plan` | 2단계: Plan | spec.md → plan.md 자동 생성 |
| `/speckit.analyze` | 2단계: Plan / 검증 | 문서 간 불일치 탐지 + 명세-코드 드리프트 탐지 |
| `/speckit.tasks` | 3단계: Task | plan.md → tasks.md 자동 생성 |
| `/speckit.implement` | 4단계: Implement | 작업 목록 기반 코드 구현 지원 |

**체크리스트 도메인 키워드**:

| 키워드 | 파일 | 주요 점검 영역 | 권장 실행 시점 |
|---|---|---|---|
| `ux` | `checklists/ux.md` | 사용자 흐름, 오류 메시지, 반응형 | clarify 후, plan 전 |
| `security` | `checklists/security.md` | 인증, 인가, 암호화, 입력값 검증 | clarify 후, plan 전 |
| `testing` | `checklists/testing.md` | 시나리오, 경계값, 엣지 케이스 | clarify 후 + implement 후 |
| `performance` | `checklists/performance.md` | 응답 시간, 부하, 캐싱 | clarify 후, plan 전 |
| `accessibility` | `checklists/accessibility.md` | WCAG, 키보드 탐색, 스크린 리더 | clarify 후, plan 전 |
| `api` | `checklists/api.md` | 엔드포인트, 에러 코드, rate limit | clarify 후, plan 전 |
| _(없음)_ | `checklists/requirements.md` | 전반적인 요구사항 완성도 | specify 시 자동 생성 |

**implement 후 테스트 방법 요약**:

| 방법 | 명령어 | 역할 | 추천 상황 |
|---|---|---|---|
| 방법 1 | `/speckit.checklist testing` | 수용 기준 충족 여부 시나리오 점검 | 기능 완료 후 빠른 검증 |
| 방법 2 | `/speckit.analyze` 재실행 | 명세-코드 드리프트 탐지 | 변경 반영 후 일관성 확인 |
| 방법 3 | `constitution.md` 테스트 원칙 | implement 시 테스트 코드 자동 생성 | 모든 팀 프로젝트 (프로젝트 시작 시 1회) |

### 아티팩트 요약

| 파일 | 한 줄 요약 |
|---|---|
| `constitution.md` | 프로젝트 전체 기술 원칙 (변하지 않는 규칙) |
| `spec.md` | 기능이 무엇인지, 왜 필요한지 정의 |
| `plan.md` | 어떻게 만들지 기술적으로 설계 |
| `tasks.md` | 누가, 무엇을, 언제 할지 구체적 작업 목록 |
| `checklists/*.md` | 도메인별 명세 품질 검사 결과 및 구현 후 검증 결과 |

### 전체 흐름 한눈에 보기

```
프로젝트 시작
    │
    ├── constitution.md 작성 (팀 기술 원칙 + 테스트 원칙 합의)
    │
    └── 기능별 반복:
        │
        ├── /speckit.specify      →  spec.md 생성
        │                            └── checklists/requirements.md 자동 생성
        ├── /speckit.clarify      →  모호함 해소 → spec.md 보완
        ├── /speckit.checklist    →  도메인별 품질 점검 → spec.md 보완
        │   (ux / security /         checklists/ux.md, security.md 등 생성
        │    testing / api 등)        ↑ testing 도메인: 테스트 시나리오 명세 점검
        ├── /speckit.plan         →  plan.md 생성
        ├── /speckit.analyze      →  불일치 탐지 → 수정 → 재검사
        ├── /speckit.tasks        →  tasks.md 생성
        ├── /speckit.implement    →  코드 구현 (수용 기준과 1:1 대응)
        │                            ← constitution.md 테스트 원칙 적용 시
        │                               테스트 코드도 함께 자동 생성
        │
        └── [구현 후 검증] ← 9절 참조
            ├── /speckit.checklist testing  →  수용 기준 충족 확인
            │                                   checklists/testing.md 업데이트
            ├── /speckit.analyze (재실행)   →  명세-코드 드리프트 점검
            └── Git 커밋 (코드 + 테스트 + 체크리스트 함께)
```

---

## 참고 자료

- [GitHub Spec Kit 공식 저장소](https://github.com/github/spec-kit)
- [GitHub Copilot 공식 문서](https://docs.github.com/copilot)
- [GitHub Education (학생 무료 혜택)](https://education.github.com)
- [VS Code 다운로드](https://code.visualstudio.com)
- [GitHub Codespaces 소개](https://github.com/features/codespaces)
- [애자일 사용자 스토리 작성법](https://www.atlassian.com/agile/project-management/user-stories)
- [GitHub Spec Kit Issues #1745 — /speckit.verify 추가 요청](https://github.com/github/spec-kit/issues/1745)
- [GitHub Spec Kit Issues #1323 — /speckit.review 추가 제안](https://github.com/github/spec-kit/issues/1323)

---

*명령어 기준: GitHub Spec Kit 공식 문서 (2026년 5월 기준)*
