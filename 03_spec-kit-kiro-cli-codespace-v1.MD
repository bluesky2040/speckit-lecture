# GitHub Spec Kit 실습 가이드 — 3차시 (Kiro CLI 버전)
## 주제: 로그인 화면 만들기 + 명세 수정 + 기능 추가

> **이 실습은 3차시입니다.**  
> 1차시(SDD 방법론) → 2차시(Spec Kit 가이드) 에서 배운 개념을 직접 실행합니다.  
> 이 버전은 **Kiro CLI (AWS)** 를 사용합니다. Gemini CLI 버전과 SDD 워크플로는 동일하며,  
> 도구와 인증 방식만 다릅니다.

**목표**: GitHub Codespace 환경에서 SDD 전체 흐름을 체험하고, 로그인 화면을 완성한다  
**사용 도구**: GitHub Codespaces, Kiro CLI, specify CLI  
**결과물**: HTML + CSS + JavaScript 기반 로그인 화면 (명세 변경 + 3가지 기능 포함)  
**무료 여부**: AWS Builder ID 로 Kiro CLI 를 **무료**로 사용 (신용카드 불필요)

---

## Gemini CLI 버전과의 차이점 요약

| 항목 | Gemini CLI 버전 | **Kiro CLI 버전 (이 문서)** |
|---|---|---|
| 필요 계정 | Google 계정 | **AWS Builder ID** (무료 가입) |
| AI 도구 설치 | `npm install -g @google/gemini-cli` | **zip 파일 다운로드 + 설치 스크립트** |
| AI 채팅 실행 | `gemini` | **`kiro-cli`** |
| 명령어 파일 위치 | `.gemini/commands/*.toml` | **`.kiro/skills/*/SKILL.md`** |
| Constitution 파일 | `.specify/memory/constitution.md` | **`.kiro/steering/constitution.md`** |
| 슬래시 명령어 형식 | `/speckit.specify` (점) | **`/speckit-specify` (하이픈)** |
| SDD 워크플로 | 동일 | **동일** |

> **핵심 메시지**: 도구가 달라도 SDD 방법론과 워크플로는 완전히 동일하다.  
> 명령어 이름만 점(`.`) → 하이픈(`-`) 으로 바뀐다.

---

## 목차

1. [사전 확인 — Kiro CLI 경로 안내](#사전-확인--kiro-cli-경로-안내)
2. [실습 전 준비 사항](#실습-전-준비-사항)
3. [실습 개요](#실습-개요)
4. [Part 1. Codespace 생성](#part-1-codespace-생성-약-15분)
5. [Part 2. 도구 설치](#part-2-도구-설치-약-15분)
6. [Part 3. 프로젝트 초기화](#part-3-프로젝트-초기화-약-10분)
7. [Part 4. 기본 로그인 화면 구현](#part-4-sdd-실습-1단계--기본-로그인-화면-약-65분)
8. [Part 5. 결과물 실행 및 테스트](#part-5-결과물-실행-및-테스트-약-15분)
9. [Part 6. 기존 명세(001) 변경 대처](#part-6-sdd--기존-명세001-변경-대처-약-30분)
10. [Part 7. 로그인 횟수 제한 추가](#part-7-sdd-실습-2단계--로그인-횟수-제한-추가-약-30분)
11. [Part 8. 아이디 저장 기능 추가](#part-8-sdd-실습-3단계--아이디-저장-기능-추가-약-30분)
12. [Part 9. 전체 산출물 구조 확인](#part-9-전체-산출물-구조-확인)
13. [트러블슈팅](#트러블슈팅)
14. [전체 명령어 요약표](#전체-명령어-요약표)
15. [실습 진행 시간 배분](#실습-진행-시간-배분)

---

## 사전 확인 — Kiro CLI 경로 안내

`specify init` 실행 시 AI 에이전트별로 생성되는 폴더 경로와 파일 형식이 다르다.  
이 실습은 **Kiro CLI** 를 사용하므로 아래 경로를 확인한다.

| 에이전트 | 명령어(스킬) 파일 위치 | 파일 형식 |
|---|---|---|
| **Kiro CLI** | `.kiro/skills/*/SKILL.md` | **Markdown + YAML frontmatter** |
| Gemini CLI | `.gemini/commands/` | `.toml` |
| GitHub Copilot | `.github/prompts/` | `.prompt.md` |
| Claude Code | `.claude/commands/` | `.md` |

> **Kiro CLI 의 스킬(Skill) 시스템**  
> Kiro CLI 는 `.kiro/skills/` 폴더의 파일을 **슬래시 명령어**로 자동 인식한다.  
> 예: `.kiro/skills/speckit-specify/SKILL.md` → `/speckit-specify` 명령으로 실행 가능.  
>
> **Gemini CLI 와의 명령어 이름 차이**  
> Kiro 의 스킬 이름 규칙은 **소문자 + 하이픈만 허용** (점(.) 불가)이다.  
> 따라서 `/speckit.specify` (Gemini) → `/speckit-specify` (Kiro) 로 변경된다.

### Kiro Steering 파일 (Constitution 대응)

Kiro CLI 에는 `constitution.md` 대신 **Steering 파일** 이 있다.  
Steering 파일은 `.kiro/steering/` 폴더에 저장되며, AI가 항상 참조하는 배경 지식이다.

```
.kiro/
├── skills/          ← 슬래시 명령어 정의 (speckit 명령어)
└── steering/        ← 프로젝트 원칙 및 맥락 (constitution.md 대응)
    └── constitution.md   ← inclusion: always 로 항상 로드됨
```

---

## 실습 전 준비 사항

| 필요 계정 | 가입 주소 | 비고 |
|---|---|---|
| GitHub 계정 | https://github.com/signup | Codespace 실행에 필요 |
| **AWS Builder ID** | **https://profile.aws.amazon.com** | **Kiro CLI 인증에 사용 (무료, 신용카드 불필요)** |

> **AWS Builder ID 란?**  
> AWS 서비스 이용 없이 무료로 만들 수 있는 개인 개발자 계정이다.  
> AWS 계정(유료)과는 다르다. 이메일로만 가입 가능하며, Kiro CLI 를 무료로 사용할 수 있다.

**브라우저**: Chrome 또는 Edge 권장 (팝업 허용 필수)

---

## 실습 개요

SDD(Spec-Driven Development)는 코드를 먼저 짜지 않는다.  
**무엇을, 왜 만드는지** 먼저 정의하고, AI 에이전트가 구현하도록 지시하는 방법론이다.

### SDD 전체 흐름

```
constitution → specify → clarify → checklist → plan → tasks → analyze → implement
(원칙 설정)  (요구사항) (모호함 제거) (명세점검)  (기술설계) (작업목록) (최종점검)  (코드생성)
   1회만        ①         ②            ③         ④        ⑤          ⑥          ⑦
```

> **Kiro CLI 명령어 대응표**
>
> | Gemini CLI | Kiro CLI |
> |---|---|
> | `/speckit.constitution` | `/speckit-constitution` |
> | `/speckit.specify` | `/speckit-specify` |
> | `/speckit.clarify` | `/speckit-clarify` |
> | `/speckit.checklist` | `/speckit-checklist` |
> | `/speckit.plan` | `/speckit-plan` |
> | `/speckit.tasks` | `/speckit-tasks` |
> | `/speckit.analyze` | `/speckit-analyze` |
> | `/speckit.implement` | `/speckit-implement` |

> **constitution.md 가 테스트와 연결되는 원리 (Kiro CLI 버전)**  
> Kiro CLI 에서 `.kiro/steering/constitution.md` 는 **`inclusion: always`** 로 설정되어  
> 모든 채팅 세션에서 자동으로 로드된다.  
> 테스트 원칙을 이 파일에 명시하면, `/speckit-checklist testing` 실행 시  
> Kiro 가 steering 파일을 참조하여 **테스트 기준을 자동으로 반영**한다.

### 기능을 추가할 때의 흐름

```
[최초 구현]  constitution → specify(001) → clarify → checklist → plan → tasks → analyze → implement
[기능 추가]               → specify(002) → clarify → checklist → plan → tasks → analyze → implement
[기능 추가]               → specify(003) → clarify → checklist → plan → tasks → analyze → implement
```

### 기존 명세를 수정할 때의 흐름

```
[명세 수정]  spec.md 직접 수정 → clarify → checklist → analyze → plan(재실행) → tasks(재실행) → implement
```

### 이번 실습에서 다루는 시나리오

| 파트 | 시나리오 | SDD 번호 |
|---|---|---|
| Part 4 | 기본 로그인 화면 구현 | 001 최초 구현 |
| Part 6 | 001 명세 변경 — 로그인 성공 후 동작 추가 | 001 명세 수정 |
| Part 7 | 로그인 횟수 제한 + 카운트다운 기능 추가 | 002 신규 추가 |
| Part 8 | 아이디 저장 (localStorage) 기능 추가 | 003 신규 추가 |

---

## Part 1. Codespace 생성 (약 15분)

### 1-1. 빈 레포지토리 만들기

1. https://github.com 에 로그인한다.
2. 오른쪽 상단 `+` 버튼 클릭 후 `New repository` 를 선택한다.
3. 아래와 같이 입력한다.

| 항목 | 입력값 |
|---|---|
| Repository name | `login-page-lab` |
| Visibility | Public 또는 Private 선택 |
| Add a README file | **반드시 체크** |

4. `Create repository` 버튼을 클릭한다.

### 1-2. Codespace 실행

1. 생성된 레포지토리 페이지에서 초록색 `Code` 버튼을 클릭한다.
2. `Codespaces` 탭을 선택한다.
3. `Create codespace on main` 을 클릭한다.
4. 브라우저에서 VS Code 화면이 열릴 때까지 1~2분 기다린다.

터미널이 보이지 않으면 상단 메뉴에서 `Terminal → New Terminal` 을 선택한다.

### 1-3. 현재 위치 확인

```bash
pwd
```

예상 출력:

```
/workspaces/login-page-lab
```

> **확인 포인트 ✅**  
> `/workspaces/login-page-lab` 이 출력되면 Part 1 완료.

---

## Part 2. 도구 설치 (약 15분)

> **Gemini CLI 와의 차이**: Gemini 는 npm 으로 설치했지만,  
> Kiro CLI 는 공식 설치 스크립트로 설치한다.

### 2-1. uv 설치

`uv` 는 Python 패키지를 빠르게 설치하는 도구다. `specify CLI` 설치에 필요하다.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc
```

설치 확인:

```bash
uv --version
```

예상 출력:

```
uv 0.x.x
```

### 2-2. specify CLI 설치

`specify CLI` 는 SDD 명령어(`/speckit-*`)를 실행해 주는 핵심 도구다.

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

확인:

```bash
specify --version
```

예상 출력:

```
specify 0.x.x
```

### 2-3. Kiro CLI 설치

Kiro CLI 는 AWS 가 제공하는 터미널 기반 AI 에이전트 도구다.  
AWS Builder ID 로 무료로 사용할 수 있으며, 신용카드가 필요하지 않다.

**설치 방법 1: 공식 설치 스크립트 (권장)**

```bash
curl --proto '=https' --tlsv1.2 -sSf \
  'https://desktop-release.q.us-east-1.amazonaws.com/latest/kirocli-x86_64-linux.zip' \
  -o kirocli.zip
unzip kirocli.zip
sudo ./install.sh
source ~/.bashrc
```

**설치 방법 2: Homebrew (macOS 또는 Linux)**

```bash
brew install kiro-cli
```

설치 확인:

```bash
kiro-cli --version
```

예상 출력:

```
kiro-cli x.x.x
```

> **`kiro-cli: command not found` 오류 시**  
> PATH 가 업데이트되지 않은 것이다.  
>
> ```bash
> export PATH="$HOME/.local/bin:$PATH"
> source ~/.bashrc
> kiro-cli --version
> ```

> **확인 포인트 ✅**  
> `uv`, `specify`, `kiro-cli` 세 가지 모두 버전이 출력되면 Part 2 완료.

---

## Part 3. 프로젝트 초기화 (약 10분)

### 3-1. specify 프로젝트 생성

현재 위치(`/workspaces/login-page-lab`) 에서 실행한다.

```bash
specify init login-project --integration kiro
```

실행 중 에이전트 선택 화면이 나오면 `kiro` 를 선택한다.

### 3-2. 프로젝트 폴더로 이동

```bash
cd login-project
```

이동 후 현재 위치 확인:

```bash
pwd
```

예상 출력:

```
/workspaces/login-page-lab/login-project
```

> **중요**: 이후 모든 터미널 명령어와 `kiro-cli` 실행은 반드시 이 폴더(`login-project`) 안에서 실행해야 한다.

### 3-3. 생성된 구조 확인

```bash
ls -la
```

예상 출력:

```
.kiro/
.specify/
README.md
```

Kiro 스킬 파일 확인:

```bash
ls .kiro/skills/
```

아래 폴더들이 모두 보이면 정상이다.

```
speckit-analyze/
speckit-checklist/
speckit-clarify/
speckit-constitution/
speckit-implement/
speckit-plan/
speckit-specify/
speckit-tasks/
```

각 폴더 안에 `SKILL.md` 파일이 있는지 확인한다.

```bash
ls .kiro/skills/speckit-specify/
```

예상 출력:

```
SKILL.md
```

`SKILL.md` 파일의 구조를 확인한다.

```bash
cat .kiro/skills/speckit-specify/SKILL.md
```

예상 출력 (일부):

```markdown
---
name: speckit-specify
description: Generate a feature specification with user stories, acceptance criteria,
             and edge cases. Use when starting a new feature or clarifying requirements.
---

## Speckit Specify

Generate a comprehensive spec.md for the given feature...
```

> **Kiro 스킬의 핵심 구조**  
> YAML frontmatter의 `name` 필드가 슬래시 명령어 이름이 된다.  
> `name: speckit-specify` → `/speckit-specify` 명령으로 실행 가능.

Steering 폴더도 확인한다.

```bash
ls .kiro/steering/
```

예상 출력 (초기에는 비어 있음):

```
(빈 폴더 또는 없음)
```

> Steering 파일은 Part 4 Step 1 에서 직접 생성한다.

`.specify` 내부도 확인한다.

```bash
ls .specify/
```

예상 출력:

```
memory/   specs/   templates/
```

### 3-4. AWS Builder ID 로 Kiro CLI 인증

반드시 `login-project` 폴더 안에서 실행한다.

```bash
kiro-cli
```

처음 실행 시 인증 요청 메시지가 나타난다.

```
To use Kiro CLI, you need to authenticate.
Press Enter to open the browser for authentication...
```

Enter 를 누르면 브라우저가 열리며 AWS Builder ID 로그인 페이지가 표시된다.

```
[Chrome 팝업이 차단된 경우]
주소창 오른쪽 팝업 차단 아이콘 클릭 → 팝업 허용
```

**AWS Builder ID 최초 가입 시**:

```
1. https://profile.aws.amazon.com 접속
2. Create an AWS Builder ID 클릭
3. 이메일 주소 입력
4. 이메일로 받은 인증 코드 입력
5. 이름, 비밀번호 설정
6. 가입 완료 후 Kiro CLI 인증 페이지에서 로그인
```

로그인이 완료되면 터미널에 채팅창이 열린다.

```
Kiro CLI  vX.X.X
Type /help for help, /exit to quit

>
```

> **이 채팅창에서 `/speckit-*` 명령어를 입력한다.**  
> 터미널 명령어(`bash`, `ls` 등)와 Kiro CLI 채팅 명령어는 서로 다른 창이다.  
> 헷갈리면: 프롬프트가 `>` 이면 Kiro 채팅창, `$` 이면 터미널이다.

### 3-5. 스킬 로드 확인

Kiro 채팅창(`>` 프롬프트)에서 사용 가능한 스킬을 확인한다.

```
> /context show
```

예상 출력 (일부):

```
Available skills:
  /speckit-analyze      - Analyze artifacts for consistency
  /speckit-checklist    - Generate quality checklist
  /speckit-clarify      - Clarify ambiguous requirements
  /speckit-constitution - Create project constitution/steering
  /speckit-implement    - Generate implementation code
  /speckit-plan         - Create implementation plan
  /speckit-specify      - Generate feature specification
  /speckit-tasks        - Generate task list
```

> **확인 포인트 ✅**  
> `/speckit-*` 명령어들이 목록에 보이면 Part 3 완료. 실습 시작 가능.

---

## Part 4. SDD 실습 1단계 — 기본 로그인 화면 (약 65분)

### 실습 목표

HTML + CSS + JavaScript 로 구성된 로그인 화면을 SDD 7단계를 거쳐 만든다.

---

### Step 1. Constitution — 프로젝트 원칙 설정 (Steering 파일 생성)

> **[SDD 기반 원칙]** — Kiro CLI 에서는 `constitution.md` 대신  
> `.kiro/steering/constitution.md` 에 Steering 파일로 저장된다.  
> `inclusion: always` 설정으로 Kiro 가 **모든 채팅에서 자동으로 참조**한다.

**Kiro 채팅창** (`>` 프롬프트) 에 아래를 입력한다.

```
/speckit-constitution
이 프로젝트는 교육용 웹 로그인 화면입니다.

[개발 원칙]
- HTML, CSS, JavaScript 만 사용한다 (외부 프레임워크 사용 금지)
- 하나의 index.html 파일로 구성한다
- 코드에 한국어 주석을 달아 초심자가 이해할 수 있도록 한다
- 모바일과 데스크탑 모두에서 보기 좋게 만든다
- 불필요하게 복잡한 구조는 피하고 단순하게 작성한다
- 모든 로그 및 AI 와의 대화는 한국어로 한다

[테스트 원칙]
- implement 완료 후 반드시 /speckit-checklist testing 으로 품질을 검증한다
- 테스트는 브라우저에서 직접 시나리오를 실행하는 방식으로 진행한다 (자동화 테스트 도구 미사용)
- 각 수용 기준(Given-When-Then)에 대응하는 테스트 시나리오를 1:1 로 검증한다
- 정상 흐름(Happy Path)과 오류 흐름(Error Path)을 모두 테스트한다
- 모바일 화면(375px 기준)에서도 동작을 확인한다
- 개발 원칙 준수 여부(단일 파일, 한국어 주석, 외부 라이브러리 미사용)도 테스트 항목에 포함한다
```

Kiro 가 Steering 파일을 생성한 후 **터미널 새 탭**에서 확인한다.

```bash
cat .kiro/steering/constitution.md
```

예상 출력 (파일 첫 부분):

```markdown
---
inclusion: always
---

# 프로젝트 헌법 (Project Constitution)

## 개발 원칙
- HTML, CSS, JavaScript 만 사용한다 (외부 프레임워크 사용 금지)
...

## 테스트 원칙
- implement 완료 후 반드시 /speckit-checklist testing 으로 품질을 검증한다
...
```

> **`inclusion: always` 의 의미**  
> Kiro CLI 가 모든 채팅 세션을 시작할 때 이 파일을 **자동으로 컨텍스트에 포함**한다.  
> Gemini CLI 의 `GEMINI.md` + `constitution.md` 역할을 동시에 수행한다.

> **확인 포인트 ✅**  
> `.kiro/steering/constitution.md` 에 `inclusion: always` 와 `[테스트 원칙]` 섹션 확인.

---

### Step 2. Specify 001 — 요구사항 정의

> **[SDD 1단계: Specify]**

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-specify
사용자가 아이디와 비밀번호를 입력하여 로그인하는 화면을 만들어줘.

[기능 요구사항]
- 아이디 입력 필드가 있다
- 비밀번호 입력 필드가 있다 (입력 내용은 별표로 가려진다)
- 로그인 버튼이 있다
- 아이디 또는 비밀번호가 비어 있으면 "아이디와 비밀번호를 입력하세요" 메시지를 표시한다
- 아이디가 'admin', 비밀번호가 '1234' 이면 "로그인 성공!" 메시지를 표시한다
- 아이디나 비밀번호가 틀리면 "아이디 또는 비밀번호가 올바르지 않습니다" 메시지를 표시한다
- 비밀번호 보이기/숨기기 버튼이 있다
```

**터미널** 에서 생성 여부를 확인한다.

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/

cat .specify/specs/001-login-page/spec.md
```

---

### Step 3. Clarify — 모호한 부분 명확하게

> **[SDD 1단계: Specify 보완]**

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-clarify
다음을 명확히 해줘:
- 화면 배경은 밝은 회색 계열로 한다
- 로그인 폼은 화면 정중앙에 카드 형태로 배치한다
- 오류 메시지는 빨간색으로 표시한다
- 성공 메시지는 초록색으로 표시한다
- 로그인 버튼은 파란색으로 한다
- 페이지 제목은 "로그인" 으로 한다
- Enter 키를 눌러도 로그인이 시도되도록 한다
```

---

### Step 4. Checklist — 명세 완성도 점검 (specify 후)

> **[SDD 1단계: Specify 최종 검증]** — `plan` 으로 넘어가기 전에  
> spec.md 에 빠진 항목이 없는지 점검한다.

#### 4-1. 기본 요구사항 체크리스트

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-checklist
체크리스트를 기준으로 spec.md 를 정밀 분석하여 결과를 보고해 줘.
누락된 항목이 있으면 어떻게 수정해야 하는지 알려줘.
```

**터미널** 에서 저장 여부를 확인한다.

```bash
cat .specify/specs/001-login-page/checklists/requirements.md
```

#### 4-2. UX 관점 체크리스트

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-checklist ux
로그인 화면의 UX 관점에서 spec.md 를 점검해줘.
빠진 UX 요구사항이 있으면 무엇을 추가해야 하는지 알려줘.
```

```bash
cat .specify/specs/001-login-page/checklists/ux.md
```

#### 4-3. 체크리스트 결과 처리

`[ ]` (미통과) 항목 발견 시:

```
1. Kiro 채팅창에서 수정 방법 확인
   예) "CHK003 항목 — spec.md 에 어떤 내용을 추가해야 해?"

2. spec.md 보완 후 체크리스트 재실행
   예) "/speckit-checklist ux"

3. 모든 항목 통과 → Step 5 로 진행
```

> **확인 포인트 ✅**  
> `checklists/requirements.md`, `checklists/ux.md` 생성 확인 → Step 5 로 진행.

---

### Step 5. Plan — 기술 설계

> **[SDD 2단계: Plan]**

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-plan
단일 HTML 파일(index.html) 하나로 모든 것을 구현한다.
CSS는 style 태그 안에 작성한다.
JavaScript는 script 태그 안에 작성한다.
외부 라이브러리나 CDN을 사용하지 않는다.
Flexbox를 사용하여 중앙 정렬한다.
```

```bash
cat .specify/specs/001-login-page/plan.md
```

---

### Step 6. Tasks — 작업 목록 생성

> **[SDD 3단계: Task]**

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-tasks
```

```bash
cat .specify/specs/001-login-page/tasks.md
```

---

### Step 7. Analyze — 최종 점검

> **[SDD 2단계: Plan 검증]**

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-analyze
```

"All checks passed" 메시지가 나오면 다음 단계로 진행한다.

---

### Step 8. Implement — 코드 생성

> **[SDD 4단계: Implement]**

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-implement
```

생성 완료 후 **터미널**에서 확인한다.

```bash
ls -la
# index.html 이 생성되어 있어야 한다

cat index.html | head -20
```

---

### Step 9. Checklist — 구현 품질 검증 (implement 후)

> **[품질 게이트]** — `.kiro/steering/constitution.md` 의 `[테스트 원칙]` 이  
> Kiro 에 의해 자동으로 로드되어, 체크리스트 생성 시 자동 반영된다.

#### 9-1. Steering 기반 테스트 체크리스트 실행

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-checklist testing
생성된 index.html 이 spec.md 의 모든 수용 기준을 충족하는지
테스트 시나리오 관점에서 점검해줘.
.kiro/steering/constitution.md 의 [테스트 원칙] 도 함께 반영해서 체크리스트를 만들어줘.
누락되거나 구현되지 않은 기능이 있으면 알려줘.
```

```bash
cat .specify/specs/001-login-page/checklists/testing.md
```

예상 출력 (일부):

```markdown
# 테스트 체크리스트: 기본 로그인 화면 (implement 후)
# .kiro/steering/constitution.md [테스트 원칙] 기반 생성

## 수용 기준 검증 (spec.md 기반)

### 정상 흐름 (Happy Path)
- [ ] CHK001 - admin/1234 입력 → "로그인 성공!" 초록색 메시지가 표시되는가?
- [ ] CHK002 - 로그인 성공 후 입력 필드가 초기화되는가?

### 오류 흐름 (Error Path)
- [ ] CHK003 - 빈 칸 제출 → "아이디와 비밀번호를 입력하세요" 메시지가 표시되는가?
- [ ] CHK004 - 틀린 정보 → "아이디 또는 비밀번호가 올바르지 않습니다" 메시지가 표시되는가?

## .kiro/steering/constitution.md 개발 원칙 준수 검증

- [ ] CHK008 - CSS 가 `<style>` 태그 안에 작성되어 있는가?
- [ ] CHK009 - JavaScript 가 `<script>` 태그 안에 작성되어 있는가?
- [ ] CHK010 - CDN 링크나 외부 라이브러리가 없는가?
- [ ] CHK011 - 주요 함수에 한국어 주석이 달려 있는가?

## 반응형 검증

- [ ] CHK013 - 브라우저 폭 375px 에서 로그인 카드가 잘리지 않는가?
```

#### 9-2. 브라우저에서 직접 시나리오 실행

체크리스트를 보면서 브라우저에서 각 항목을 직접 확인한다.

```
브라우저 테스트 → 미통과 항목 발견 시:

1. Kiro 채팅창에 수정 요청
   예) "index.html 에서 CHK011 - 한국어 주석이 없어. steering/constitution.md 원칙에 맞게 추가해줘."

2. Ctrl+Shift+R (강제 새로고침) 후 재확인

3. 모든 항목 통과 → Part 5 로 진행
```

> **확인 포인트 ✅**  
> `index.html` 생성 확인, `checklists/testing.md` 생성 확인, 모든 항목 체크 완료 → Part 4 완료.

---

## Part 5. 결과물 실행 및 테스트 (약 15분)

### 5-1. 브라우저에서 열기

**방법 1: Live Server 확장 프로그램 (권장)**

1. VS Code 왼쪽 사이드바에서 확장 프로그램 아이콘을 클릭한다.
2. 검색창에 `Live Server` 를 입력한다.
3. `Live Server` (작성자: Ritwick Dey) 를 설치한다.
4. 파일 탐색기에서 `index.html` 을 우클릭한다.
5. `Open with Live Server` 를 선택한다.

**방법 2: Python 내장 서버**

```bash
python3 -m http.server 8080
```

VS Code 하단 `포트(PORTS)` 탭에서 8080 포트의 지구본 아이콘을 클릭한다.

> **방법 2 사용 시 주의**: 서버를 실행한 터미널은 종료하지 않는다.  
> 이후 파트에서 기능이 추가되면 `Ctrl+Shift+R` (강제 새로고침) 으로 확인한다.

### 5-2. 기본 동작 테스트

| 시나리오 | 아이디 | 비밀번호 | 예상 결과 |
|---|---|---|---|
| 빈 칸 제출 | (비워둠) | (비워둠) | "아이디와 비밀번호를 입력하세요" |
| 로그인 성공 | admin | 1234 | "로그인 성공!" (초록색) |
| 로그인 실패 | admin | 0000 | "아이디 또는 비밀번호가 올바르지 않습니다" (빨간색) |
| 비밀번호 보이기 | 아무 값 | 아무 값 | 비밀번호 텍스트가 보임 |
| Enter 키 로그인 | admin | 1234 | Enter 키로도 로그인 성공 |

### 5-3. 예상 결과물 화면 구성

```
+------------------------------------------+
|                                          |
|   +----------------------------------+   |
|   |          로그인                  |   |
|   |  아이디                          |   |
|   |  [                          ]    |   |
|   |  비밀번호                        |   |
|   |  [                        👁]    |   |
|   |  [        로그인 버튼        ]   |   |
|   |  ← 메시지 표시 영역              |   |
|   +----------------------------------+   |
|                                          |
+------------------------------------------+
  배경: 밝은 회색     카드: 흰색 + 그림자
```

---

## Part 6. SDD — 기존 명세(001) 변경 대처 (약 30분)

### 변경 시나리오

> **기획자 요청**: "로그인 성공 후 '로그인 성공!' 메시지를 2초 동안 보여준 뒤,  
> '환영합니다, admin 님!' 으로 교체하고 비밀번호 입력 필드를 초기화해 주세요."

### 나쁜 방법 vs 올바른 SDD 방법

```
[나쁜 방법 — 코드만 바꾸는 경우]
  index.html 수정 → 배포
  → spec.md 는 여전히 기존 내용 → 명세와 코드 불일치 → 기술 부채

[올바른 SDD 방법 — 명세부터 수정]
  spec.md 수정 → clarify → checklist → analyze → plan/tasks 수정 → implement
```

---

### Step 1. spec.md 직접 수정

**터미널** 에서 spec.md 를 연다.

```bash
nano .specify/specs/001-login-page/spec.md
```

**수정 후 — 변경된 수용 기준:**

```markdown
## 수용 기준

Given: 사용자가 올바른 아이디(admin)와 비밀번호(1234)를 입력했을 때
When:  로그인 버튼을 클릭하면
Then:  "로그인 성공!" 초록색 메시지가 2초 동안 표시된다
Then:  2초 후 메시지가 "환영합니다, admin 님!" 으로 교체된다
Then:  비밀번호 입력 필드가 초기화된다 (아이디 필드는 유지)

## 변경 이력
- 2026-05-25: 로그인 성공 후 환영 메시지 및 필드 초기화 요구사항 추가 (기획팀 요청)
```

---

### Step 2. Clarify — 변경된 명세 보완

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-clarify
spec.md 가 수정되었어.
로그인 성공 후 동작 변경에 관해 다음을 명확히 해줘:
- 2초 타이머는 setTimeout 을 사용한다
- 메시지 교체 시 애니메이션(페이드) 효과는 사용하지 않는다
- 비밀번호 필드만 초기화하고 아이디 필드는 그대로 둔다
- 타이머 중복 실행 방지: 이미 타이머가 동작 중이면 새 타이머 무시
```

---

### Step 3. Checklist — 변경된 명세 점검

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-checklist
spec.md 가 수정되었어.
변경된 "로그인 성공 후 동작" 부분을 중심으로 누락된 요구사항이 없는지 점검해줘.
```

---

### Step 4. Analyze — 변경 영향 범위 탐지

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-analyze
spec.md 가 수정되었어.
변경된 내용이 plan.md 및 tasks.md 와 일치하는지 분석해줘.
```

---

### Step 5. Plan 재실행

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-plan
spec.md 가 수정되었어.
기존 index.html 에 다음 변경 사항을 추가하는 계획을 세워줘:
- 로그인 성공 시 setTimeout(2000) 으로 2초 대기
- 2초 후 메시지를 "환영합니다, admin 님!" 으로 교체
- 비밀번호 입력 필드 값을 빈 문자열로 초기화
- 타이머 중복 실행 방지
```

---

### Step 6. Tasks 재실행

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-tasks
spec.md 와 plan.md 가 수정되었어.
변경된 내용을 반영하여 tasks.md 를 업데이트해줘.
기존 Task 는 유지하고 변경에 필요한 Task 만 추가해줘.
```

---

### Step 7. Implement — 변경 코드 반영

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-implement
spec.md 가 수정되었어.
기존 index.html 에 로그인 성공 후 2초 대기 + 메시지 교체 + 비밀번호 초기화 기능을 추가해줘.
기존 코드 구조는 최대한 유지하고, 변경된 부분에는 한국어 주석으로 변경 이유를 달아줘.
```

---

### 6-2. 변경 결과 확인 및 Steering 기반 재검증

`Ctrl+Shift+R` 로 브라우저를 새로고침한다.

| 시나리오 | 예상 결과 |
|---|---|
| admin/1234 로 로그인 | "로그인 성공!" 표시 |
| 2초 대기 | "환영합니다, admin 님!" 으로 교체 |
| 비밀번호 필드 | 자동으로 비워짐 |
| 버튼 재클릭 (2초 내) | 타이머 중복 실행 없음 |

**Kiro 채팅창** 에서 변경분 재검증을 실행한다.

```
/speckit-checklist testing
spec.md 가 수정되었어.
변경된 "로그인 성공 후 동작" 부분을 중심으로
.kiro/steering/constitution.md 의 [테스트 원칙] 을 반영하여 테스트 항목을 추가해줘.
기존 testing.md 에 새 항목만 추가해줘.
```

### 6-3. Git 커밋으로 변경 이력 남기기

```bash
git add .specify/specs/001-login-page/spec.md
git add .specify/specs/001-login-page/plan.md
git add .specify/specs/001-login-page/tasks.md
git add index.html
git commit -m "feat(001): 로그인 성공 후 환영 메시지 + 필드 초기화 추가

- spec.md: 2초 후 메시지 교체 수용 기준 추가
- plan.md: setTimeout 로직 구현 계획 추가
- tasks.md: 관련 Task 항목 추가
- index.html: setTimeout, 메시지 교체, 비밀번호 초기화 구현
변경 요청: 기획팀 (2026-05-25)"
```

> **확인 포인트 ✅**  
> 변경된 동작 테스트 통과, Git 커밋 완료 → Part 6 완료.

---

## Part 7. SDD 실습 2단계 — 로그인 횟수 제한 추가 (약 30분)

```
지금부터: specify(002) → clarify → checklist(ux+security) → plan → tasks → analyze → implement
```

---

### Step 1. Specify 002

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-specify
기존 로그인 화면에 로그인 횟수 제한 기능을 추가해줘.

[기능 요구사항]
- 로그인 실패 횟수를 화면에 표시한다 (예: "로그인 시도: 1/3")
- 로그인 실패를 3회 반복하면 로그인 버튼을 비활성화한다
- 버튼이 비활성화되면 "5초 후 다시 시도하세요" 메시지를 표시한다
- 5초가 지나면 실패 횟수가 초기화되고 버튼이 다시 활성화된다
- 카운트다운 숫자가 화면에 표시된다 (5, 4, 3, 2, 1)
```

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/   002-login-attempts/
```

---

### Step 2. Clarify

```
/speckit-clarify
다음을 명확히 해줘:
- 실패 횟수 표시는 로그인 버튼 위에 작게 표시한다
- 버튼 비활성화 상태에서는 버튼 색상이 회색으로 바뀐다
- 카운트다운 메시지는 빨간색으로 표시한다
- 로그인 성공 시에는 실패 횟수가 초기화된다
- 페이지를 새로고침하면 횟수가 초기화된다 (세션 유지 불필요)
```

---

### Step 3. Checklist (UX + 보안)

**UX 체크리스트:**

```
/speckit-checklist ux
로그인 횟수 제한 기능의 UX 요구사항이 spec.md 에 완전히 정의되어 있는지 점검해줘.
```

```bash
cat .specify/specs/002-login-attempts/checklists/ux.md
```

**보안 체크리스트:**

```
/speckit-checklist security
로그인 횟수 제한 기능의 보안 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
횟수 초기화 조건, 우회 가능성 등을 중심으로 확인해줘.
```

```bash
cat .specify/specs/002-login-attempts/checklists/security.md
```

---

### Step 4. Plan

```
/speckit-plan
기존 index.html 파일을 수정하여 기능을 추가한다.
JavaScript 의 setInterval 또는 setTimeout 을 사용하여 카운트다운을 구현한다.
실패 횟수는 JavaScript 변수로 관리한다.
별도 파일을 만들지 않고 index.html 안에서 모두 처리한다.
```

---

### Step 5. Tasks

```
/speckit-tasks
```

---

### Step 6. Analyze

```
/speckit-analyze
```

---

### Step 7. Implement

```
/speckit-implement
```

---

### 7-2. 동작 테스트 + Steering 기반 검증

`Ctrl+Shift+R` 로 브라우저를 새로고침한다.

| 시나리오 | 예상 결과 |
|---|---|
| 틀린 비밀번호 1회 | "로그인 시도: 1/3" 표시 |
| 틀린 비밀번호 3회 | 버튼 비활성화(회색), 카운트다운 시작 |
| 5초 대기 | 버튼 재활성화(파란색), 횟수 초기화 |
| 올바른 정보 | "로그인 성공!" → 2초 후 환영 메시지 (Part 6 유지) |

**Kiro 채팅창** 에서 검증을 실행한다.

```
/speckit-checklist testing
횟수 제한 기능(002)이 구현된 index.html 을
.kiro/steering/constitution.md 의 [테스트 원칙] 과 spec.md 의 수용 기준에 맞게 검증해줘.
결과를 .specify/specs/002-login-attempts/checklists/testing.md 로 저장해줘.
```

```bash
cat .specify/specs/002-login-attempts/checklists/testing.md
```

> **확인 포인트 ✅** `checklists/testing.md` 생성 확인, 모든 항목 체크 완료 → Part 7 완료.

---

## Part 8. SDD 실습 3단계 — 아이디 저장 기능 추가 (약 30분)

### Step 1. Specify 003

**Kiro 채팅창** 에 아래를 입력한다.

```
/speckit-specify
기존 로그인 화면에 아이디 저장 기능을 추가해줘.

[기능 요구사항]
- 아이디 입력 필드 아래에 "아이디 저장" 체크박스가 있다
- 체크박스를 체크하고 로그인 성공하면 아이디가 브라우저에 저장된다
- 다음에 페이지를 열면 저장된 아이디가 자동으로 입력 필드에 채워진다
- 저장된 아이디가 있으면 체크박스도 자동으로 체크된 상태로 표시된다
- 체크박스를 해제하고 로그인하면 저장된 아이디가 삭제된다
```

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/   002-login-attempts/   003-save-id/
```

---

### Step 2. Clarify

```
/speckit-clarify
다음을 명확히 해줘:
- 아이디 저장에는 localStorage 를 사용한다
- 비밀번호는 저장하지 않는다
- 체크박스는 아이디 입력 필드와 비밀번호 입력 필드 사이에 배치한다
- 저장된 아이디가 있을 때는 비밀번호 입력 필드에 자동으로 포커스가 이동한다
```

---

### Step 3. Checklist (보안 + UX)

**보안 체크리스트:**

```
/speckit-checklist security
아이디 저장 기능의 보안 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
localStorage 사용, 비밀번호 미저장 원칙 등을 중심으로 확인해줘.
```

```bash
cat .specify/specs/003-save-id/checklists/security.md
```

**UX 체크리스트:**

```
/speckit-checklist ux
아이디 저장 기능의 UX 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
체크박스 위치, 자동 입력 동작, 포커스 이동 등을 중심으로 확인해줘.
```

```bash
cat .specify/specs/003-save-id/checklists/ux.md
```

---

### Step 4. Plan

```
/speckit-plan
localStorage 를 사용하여 아이디를 브라우저에 저장한다.
저장 키 이름은 'saved_username' 으로 한다.
페이지 로드 시 localStorage 를 확인하여 저장된 아이디를 불러온다.
별도 파일 없이 index.html 안에서 처리한다.
```

---

### Step 5. Tasks

```
/speckit-tasks
```

---

### Step 6. Analyze

```
/speckit-analyze
```

---

### Step 7. Implement

```
/speckit-implement
```

---

### 8-2. 동작 테스트 + Steering 기반 검증

`Ctrl+Shift+R` 로 브라우저를 새로고침한다.

| 시나리오 | 예상 결과 |
|---|---|
| 체크박스 체크 후 로그인 성공 | localStorage 에 아이디 저장 |
| 페이지 새로고침 | 아이디 자동 입력, 체크박스 자동 체크 |
| 저장된 상태에서 비밀번호 필드 | 자동으로 포커스 이동 |
| 체크박스 해제 후 로그인 | localStorage 에서 아이디 삭제 |

**localStorage 저장 확인** (브라우저 개발자 도구):

```
F12 → Application 탭 → Local Storage → 'saved_username' 키 확인
```

**Kiro 채팅창** 에서 검증을 실행한다.

```
/speckit-checklist testing
아이디 저장 기능(003)이 구현된 index.html 을
.kiro/steering/constitution.md 의 [테스트 원칙] 과 spec.md 의 수용 기준에 맞게 검증해줘.
결과를 .specify/specs/003-save-id/checklists/testing.md 로 저장해줘.
```

> **확인 포인트 ✅** `checklists/testing.md` 생성 확인, 모든 항목 체크 완료 → Part 8 완료.

---

## Part 9. 전체 산출물 구조 확인

실습이 모두 끝나면 **터미널** 에서 전체 구조를 확인한다.

```bash
find . -not -path '*/\.*' | sort
```

예상 디렉터리 구조:

```
login-project/
├── .kiro/
│   ├── skills/                          ← Kiro CLI 슬래시 명령어 정의
│   │   ├── speckit-analyze/
│   │   │   └── SKILL.md
│   │   ├── speckit-checklist/
│   │   │   └── SKILL.md
│   │   ├── speckit-clarify/
│   │   │   └── SKILL.md
│   │   ├── speckit-constitution/
│   │   │   └── SKILL.md
│   │   ├── speckit-implement/
│   │   │   └── SKILL.md
│   │   ├── speckit-plan/
│   │   │   └── SKILL.md
│   │   ├── speckit-specify/
│   │   │   └── SKILL.md
│   │   └── speckit-tasks/
│   │       └── SKILL.md
│   └── steering/
│       └── constitution.md              ← inclusion: always (항상 자동 로드)
├── .specify/
│   ├── memory/
│   └── specs/
│       ├── 001-login-page/
│       │   ├── spec.md
│       │   ├── plan.md
│       │   ├── tasks.md
│       │   └── checklists/
│       │       ├── requirements.md
│       │       ├── ux.md
│       │       └── testing.md
│       ├── 002-login-attempts/
│       │   ├── spec.md
│       │   ├── plan.md
│       │   ├── tasks.md
│       │   └── checklists/
│       │       ├── ux.md
│       │       ├── security.md
│       │       └── testing.md
│       └── 003-save-id/
│           ├── spec.md
│           ├── plan.md
│           ├── tasks.md
│           └── checklists/
│               ├── security.md
│               ├── ux.md
│               └── testing.md
└── index.html
```

### .kiro/steering/constitution.md 최종 확인

```bash
cat .kiro/steering/constitution.md
```

반드시 아래 내용이 포함되어 있어야 한다.

```
✅ frontmatter: inclusion: always
✅ [개발 원칙] 섹션
✅ [테스트 원칙] 섹션
```

### 최종 완성 기능 전체 테스트

```
기본 로그인 (001 — Part 6 수정 포함)
[ ] 빈 칸 제출 → 안내 메시지
[ ] admin/1234 → "로그인 성공!" → 2초 후 환영 메시지
[ ] 비밀번호 필드 자동 초기화 (아이디 유지)
[ ] 틀린 정보 → 오류 메시지
[ ] 비밀번호 보이기/숨기기
[ ] Enter 키 로그인

로그인 횟수 제한 (002)
[ ] 실패 시 "로그인 시도: N/3" 표시
[ ] 3회 실패 → 버튼 비활성화 (회색)
[ ] 카운트다운 5→1
[ ] 5초 후 재활성화

아이디 저장 (003)
[ ] 체크박스 체크 + 로그인 성공 → 아이디 저장
[ ] 새로고침 → 아이디 자동 입력, 체크박스 자동 체크
[ ] 체크박스 해제 + 로그인 → 아이디 삭제
```

> **확인 포인트 ✅ — 실습 완주 기준**
> 1. 각 기능 폴더에 `checklists/testing.md` 파일이 존재한다
> 2. `.kiro/steering/constitution.md` 에 `inclusion: always` 와 `[테스트 원칙]` 이 있다
> 3. `001/spec.md` 에 Part 6 의 변경 이력이 기록되어 있다

---

## 실습 종료 후 Codespace 정지 방법

**방법 1 (VS Code 화면에서)**

화면 왼쪽 하단의 `>< Codespaces` → `Stop Current Codespace`.

**방법 2 (GitHub 웹에서)**

https://github.com/codespaces → 본인 Codespace 오른쪽 `...` → `Stop codespace`.

---

## 트러블슈팅

### kiro-cli: command not found

PATH 가 업데이트되지 않은 것이다.

```bash
export PATH="$HOME/.local/bin:$PATH"
source ~/.bashrc
kiro-cli --version
```

그래도 안 되면 재설치한다.

```bash
curl --proto '=https' --tlsv1.2 -sSf \
  'https://desktop-release.q.us-east-1.amazonaws.com/latest/kirocli-x86_64-linux.zip' \
  -o kirocli.zip
unzip -o kirocli.zip
sudo ./install.sh
source ~/.bashrc
```

---

### Kiro CLI 인증 팝업이 뜨지 않는다

Chrome 팝업 차단 아이콘 → **팝업 허용** 으로 변경한다.  
이후 `kiro-cli` 를 다시 실행한다.

---

### AWS Builder ID 가입이 안 된다

https://profile.aws.amazon.com 에서 직접 가입한다.  
이메일 인증이 필요하다. 스팸 폴더도 확인한다.

---

### `/speckit-*` 명령어가 목록에 없다 (`/context show` 에 미표시)

Kiro CLI 를 반드시 `login-project` 폴더 안에서 실행했는지 확인한다.

```bash
# 채팅창 종료
/exit

# 위치 확인
pwd
# .../login-project 가 출력되어야 한다

# 스킬 파일 확인
ls .kiro/skills/

# 다시 실행
kiro-cli
```

---

### .kiro/skills/ 폴더가 없다

`specify init` 이 kiro 연동 없이 실행된 것이다.

```bash
specify integration install kiro
ls .kiro/skills/
```

---

### 스킬 파일은 있지만 명령어가 인식되지 않는다

`SKILL.md` 의 `name` 필드를 확인한다.  
Kiro 스킬 이름은 **소문자, 숫자, 하이픈만** 허용된다.

```bash
cat .kiro/skills/speckit-specify/SKILL.md | head -5
```

예상 출력:

```yaml
---
name: speckit-specify
description: ...
---
```

`name` 필드에 점(`.`) 이나 대문자가 있으면 인식되지 않는다.

---

### index.html 이 생성되지 않았다

Kiro 채팅창에서 직접 요청한다.

```
/speckit-implement 를 실행했지만 index.html 이 생성되지 않았어.
.specify/specs/001-login-page/ 폴더의 spec.md, plan.md, tasks.md 를 참고해서
index.html 을 현재 폴더(login-project/)에 생성해줘.
```

---

### .kiro/steering/ 폴더가 없다

폴더를 직접 생성한 후 constitution.md 를 만든다.

```bash
mkdir -p .kiro/steering
cat > .kiro/steering/constitution.md << 'EOF'
---
inclusion: always
---

# 프로젝트 헌법

## 개발 원칙
- HTML, CSS, JavaScript 만 사용한다
- 하나의 index.html 파일로 구성한다
- 코드에 한국어 주석을 달아 초심자가 이해할 수 있도록 한다
- 모바일과 데스크탑 모두에서 보기 좋게 만든다

## 테스트 원칙
- implement 완료 후 /speckit-checklist testing 으로 품질을 검증한다
- 테스트는 브라우저에서 직접 시나리오를 실행한다
- 각 수용 기준에 대응하는 테스트 시나리오를 1:1 로 검증한다
- 모바일 화면(375px 기준)에서도 동작을 확인한다
EOF

cat .kiro/steering/constitution.md
```

---

### `/speckit-checklist testing` 결과가 일반적이다 (Steering 원칙 미반영)

`inclusion: always` 설정이 있는지 확인한다.

```bash
head -5 .kiro/steering/constitution.md
```

`inclusion: always` 가 없으면 추가한다.

```bash
# 파일 맨 앞에 frontmatter 추가
nano .kiro/steering/constitution.md
```

첫 3줄이 다음과 같아야 한다.

```
---
inclusion: always
---
```

이후 Kiro CLI 를 재시작한다.

```bash
/exit
kiro-cli
```

재시작 후 `/context show` 로 steering 파일이 로드되었는지 확인한다.

```
> /context show
```

`constitution.md (steering)` 항목이 보이면 정상이다.

---

### spec.md 수정 후 plan.md 가 자동으로 바뀌지 않는다

`/speckit-plan` 을 명시적으로 재실행한다.  
`/speckit-analyze` 는 불일치를 탐지만 하고 자동으로 파일을 수정하지 않는다.

```
[올바른 순서]
spec.md 수정 → /speckit-analyze → /speckit-plan → /speckit-tasks
```

---

### 브라우저에서 화면이 열리지 않는다 (Codespace 포트 포워딩)

VS Code 하단 `포트(PORTS)` 탭 → 8080 포트 지구본 클릭.  
목록에 없으면 `포트 추가` → `8080` 입력.  
가시성이 `Private` 이면 `Public` 으로 변경.

---

## 전체 명령어 요약표

| 단계 | 위치 | 명령어 |
|---|---|---|
| uv 설치 | 터미널 | `curl -LsSf https://astral.sh/uv/install.sh \| sh && source ~/.bashrc` |
| specify 설치 | 터미널 | `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git` |
| Kiro CLI 설치 | 터미널 | (위 2-3절 참조) |
| 프로젝트 생성 | 터미널 | `specify init login-project --integration kiro` |
| 폴더 이동 | 터미널 | `cd login-project` |
| 스킬 파일 확인 | 터미널 | `ls .kiro/skills/` |
| 스킬 로드 확인 | Kiro 채팅 | `/context show` |
| 채팅창 실행 | 터미널 | `kiro-cli` |
| 원칙 설정 (1회만) | Kiro 채팅 | `/speckit-constitution` |
| Steering 확인 | 터미널 | `cat .kiro/steering/constitution.md` |
| 요구사항 정의 | Kiro 채팅 | `/speckit-specify` |
| 모호함 제거 | Kiro 채팅 | `/speckit-clarify` |
| 명세 점검 — 기본 | Kiro 채팅 | `/speckit-checklist` |
| 명세 점검 — UX | Kiro 채팅 | `/speckit-checklist ux` |
| 명세 점검 — 보안 | Kiro 채팅 | `/speckit-checklist security` |
| 구현 후 품질 검증 | Kiro 채팅 | `/speckit-checklist testing` |
| 기술 설계 | Kiro 채팅 | `/speckit-plan` |
| 작업 목록 | Kiro 채팅 | `/speckit-tasks` |
| 일관성 점검 | Kiro 채팅 | `/speckit-analyze` |
| 코드 생성 | Kiro 채팅 | `/speckit-implement` |
| 서버 실행 | 터미널 | `python3 -m http.server 8080` |
| 강제 새로고침 | 브라우저 | `Ctrl+Shift+R` |

### Gemini CLI vs Kiro CLI 명령어 대조표

| Gemini CLI | Kiro CLI |
|---|---|
| `gemini` | `kiro-cli` |
| `/speckit.constitution` | `/speckit-constitution` |
| `/speckit.specify` | `/speckit-specify` |
| `/speckit.clarify` | `/speckit-clarify` |
| `/speckit.checklist` | `/speckit-checklist` |
| `/speckit.plan` | `/speckit-plan` |
| `/speckit.tasks` | `/speckit-tasks` |
| `/speckit.analyze` | `/speckit-analyze` |
| `/speckit.implement` | `/speckit-implement` |
| `.gemini/commands/*.toml` | `.kiro/skills/*/SKILL.md` |
| `.specify/memory/constitution.md` | `.kiro/steering/constitution.md` |

**명세 변경 시 명령어 순서**:

```
spec.md 수정 (직접 편집)
    → /speckit-clarify   (변경 내용 보완)
    → /speckit-checklist (변경된 명세 점검)
    → /speckit-analyze   (plan.md·tasks.md 불일치 탐지)
    → /speckit-plan      (plan.md 재생성)
    → /speckit-tasks     (tasks.md 재생성)
    → /speckit-implement (코드 반영)
    → git commit         (명세+코드 함께 커밋)
```

---

## 실습 진행 시간 배분

| 파트 | 내용 | 시간 |
|---|---|---|
| Part 1 | Codespace 생성 | 15분 |
| Part 2 | 도구 설치 (uv, specify, **Kiro CLI**) | **15분** |
| Part 3 | 프로젝트 초기화 및 **AWS Builder ID 인증** | 10분 |
| Part 4 | 기본 로그인 화면 구현 (SDD 7단계) | 65분 |
| Part 5 | 결과물 실행 및 테스트 | 15분 |
| Part 6 | 기존 명세 변경 대처 | 30분 |
| Part 7 | 로그인 횟수 제한 추가 | 30분 |
| Part 8 | 아이디 저장 기능 추가 | 30분 |
| Part 9 | 전체 구조 확인 및 완료 테스트 | 10분 |
| **합계** | | **약 3시간 40분** |

> **강사 운영 팁**  
> **AWS Builder ID 가입은 실습 전날** 학생들에게 미리 안내한다.  
> 이메일 인증이 필요하므로 당일 처음 가입하면 시간이 지체될 수 있다.  
>
> Kiro CLI 설치 (Part 2-3) 는 네트워크 환경에 따라 편차가 있다.  
> 사전에 Codespace 를 생성하고 도구까지 설치해 두면 Part 4 부터 바로 시작 가능하다.  
>
> Part 6·7·8 은 시간이 부족하면 과제로 전환 가능하다.

---

*본 실습 자료는 GitHub Spec Kit 공식 문서와 Kiro CLI 공식 문서를 기반으로 작성되었습니다.*  
*참고: https://github.com/github/spec-kit / https://kiro.dev/docs/cli/*  
*명령어 기준: Spec Kit 0.8.5 이상 / Kiro CLI 최신 버전 / Kiro 스킬 형식 (`.kiro/skills/*/SKILL.md`)*
