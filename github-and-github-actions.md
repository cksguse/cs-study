# GitHub와 GitHub Actions 기초 정리

## 1. GitHub란?
Git(분산형 버전 관리 시스템)을 클라우드상에서 호스팅하여 코드 저장, 버전 관리, 팀 현업 기능을 제공하는 웹 플랫폼.
- **Git** : 로컬 PC 등에서 코드의 변경 이력을 기록하는 도구
- **GitHub** : Git으로 관리되는 코드와 이력을 클라우드에 보관하고 공유하는 서비스

### 주요 기능
- **Repository(리포지토리)** : 프로젝트의 코드와 변경 이력이 저장되는 공간
- **Push** : 내 컴퓨터(Local)의 변경 사항을 깃허브 서버로 전송하는 동작
- **Pull Request** : 내가 작업한 코드 변경 사항을 검토(Review) 받고, 메인 브런치에 병합을 요청하는 기능. "내가 푸시한 브랜치의 코드를 메인 브랜치에 합쳐주세요"라고 요청하는 게시글/이벤트
- **Issues** : 버그 제보, 기능 개발 제안, ToDo 등을 관리하는 게시판 기능
---
## 2. GitHub Actions란?
GitHub 리포지토리 안에서 **빌드, 테스트, 배포 등의 워크플로우를 자동화하는 CI/CD(지속적 통합/지속적 배포) 플랫폼**
코드를 `push`하거나 `pull_request`를 생성하는 등의 이벤트를 감지하여, 미리 정의해 둔 작업 프로세스를 자동으로 실행

---
## 3. GitHub Actions의 핵심 구성 요소
GitHub Actions는 아래와 같은 계층 구조로 동작
```text
Workflow(전체 자동화 프로세스)
  └── Event (실행 트리거)
  └── Jobs (실행 환경과 작업 그룹)
        └── Steps (순차적으로 실행되는 개별 작업)
              └── Actions 또는 Run (재사용 명령어 / 컴포넌트)
```
- **Workflow** : 자동화 프로세스 전체. 리포지토리의 `.github/workflows/` 디렉토리 내에 YAML 형식으로 작성
- **Event** : 워크플로우를 실행시키는 계기 (예 : `push`,`pull_request`, 정해진 시간마다 실행하는 스케줄 등)
- **Job** : 동일한 가상 환경(Runner)에서 실행되는 Step들의 집합. 여러 Job을 병렬 또는 순차적으로 실행 가능
- **Step** : Job 내부에서 순서대로 실행되는 개별 명령 단위. 터미널 명령어(`run`)를 직접 실행되거나 작성된 Action을 불러와 사용
- **Action** : 자주 쓰이는 동작(코드 끌어오기, 언어 환경 셋팅 등)을 모아둔 재사용 가능한 최소 단위의 모듈
- **Runner** : 워크플로우가 실제로 실행되는 가상 서버(GitHub가 제공하는 Ubuntu, macOS, Windows 등)
---
## 4. 헷갈리기 쉬운 개념들
- **1. 깃(Git)을 다운받은 적이 없는데 원래 컴퓨터에 있는걸까?**
Mac을 사용중이라면 원래 들어있거나 자동으로 설치되어 있을 확률이 높음
터미널에서 처음 `git` 명령어를 입력했거나, VS Code, Salesforce CLI 같은 개발 툴을 설치할 때 Mac이 Git을 자동으로 설치해줌
- **2. Job과 Action 등은 어떤 형태인가?**

| 요소 | 실제 모습 (구현체) | 언어 / 파일 형태 |
| :--- | :--- | :--- |
| **Event** | 워크플로우 실행을 감지하고 감시하는 트리거 조건 | `.yml` (`on:` 키워드 구문) |
| **Job** | 가상 머신(Runner) 할당 지시 | `.yml` (YAML 구조) |
| **Step** | 쉘 명령어 또는 Action 호출 단락 | `.yml` (YAML 내부의 배열) |
| **Action** | **진짜 실행되는 로직/프로그램** | `index.js` (JavaScript) / `Dockerfile` / `action.yml` |
- **3. `.yml`파일의 내부 구조**
```YAML
# 1. Event (언제 실행할지 지정)
on:
  push:
    branches: [ "main" ]

# 2. Job (어떤 가상 서버에서 실행할지 지정)
jobs:
  build-and-test:
    runs-on: ubuntu-latest

    # 3. Step (그 서버 안에서 순서대로 실행할 작업 목록)
    steps:
      - name: 코드 가져오기
        uses: actions/checkout@v4  # <-- 외부 Action(모듈)을 불러와 사용

      - name: 테스트 실행
        run: npm test              # <-- 쉘 명령어를 직접 실행
```
**하나의 파일 내부에 위치** : `Event`, `Job`, `Step`  
**외부 파일/모듈을 참조** : `Action`
