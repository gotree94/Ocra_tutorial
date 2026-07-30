# Orca (ONORCA) 완벽 활용 가이드

> **Orca**는 다중 AI 코딩 에이전트를 병렬로 실행하는 **ADE (Agent Development Environment)** 입니다.
> Claude Code, Codex, OpenCode, Cursor CLI, Grok, Gemini 등 모든 CLI 에이전트를 하나의 IDE에서 side-by-side로 실행하세요.
>
> GitHub: https://github.com/stablyai/orca | 문서: https://www.onorca.dev/docs

---

## 목차

1. [Orca란?](#1-orca란)
2. [설치](#2-설치)
3. [첫 3-에이전트 세션 시작하기](#3-첫-3-에이전트-세션-시작하기)
4. [에이전트 연결하기](#4-에이전트-연결하기)
5. [핵심 기능](#5-핵심-기능)
6. [고급 설정](#6-고급-설정)
7. [Worktree & 병렬 작업](#7-worktree--병렬-작업)
8. [오케스트레이션 (자동화)](#8-오케스트레이션-자동화)
9. [기획 → 코드 → Git 워크플로우 자동화](#9-기획--코드--git-워크플로우-자동화)
10. [팁 & 문제 해결](#10-팁--문제-해결)

---

## 1. Orca란?

### 1.1 개요

Orca는 "에이전트를 위한 IDE"입니다. 기존 IDE가 개발자 한 명을 위해 설계되었다면, Orca는 **개발자와 여러 AI 에이전트가 함께 작업**하도록 설계되었습니다.

**핵심 개념:**
- 각 작업마다 **격리된 git worktree** 생성
- 여러 에이전트를 **동시에 실행**하며 결과 비교
- **Design Mode**로 브라우저 UI 실시간 수정
- **모바일 앱**으로 원격에서 에이전트 모니터링
- **SSH Worktree**로 원격 서버에서 에이전트 실행

### 1.2 Orca vs 다른 도구

| 특징 | Orca | 일반 IDE | 일반 터미널 |
|------|------|----------|------------|
| 다중 에이전트 병렬 실행 | ✅ 격리된 worktree | ❌ | ❌ |
| git worktree 자동 관리 | ✅ | ❌ | 수동 |
| Design Mode (브라우저) | ✅ 내장 Chromium | 부분적 | ❌ |
| 모바일 앱 | ✅ iOS/Android | ❌ | ❌ |
| Diff 리뷰 & 주석 | ✅ | 별도 도구 필요 | ❌ |
| 오케스트레이션 | ✅ Run/Task/DAG | ❌ | ❌ |
| 비용 | **무료** (MIT 라이선스) | 유료 | - |

### 1.3 Orca가 아닌 것

- **AI 모델이 아닙니다** — 사용자가 직접 Claude, GPT, Gemini 등의 구독을 가져옵니다
- **Git 대체가 아닙니다** — 각 worktree는 실제 git worktree이며, 직접 `cd`해서 git 명령어 사용 가능
- **호스팅 VPS가 아닙니다** — 기본적으로 데스크톱에서 실행, SSH/자체 서버로 확장

---

## 2. 설치

### 2.1 macOS

```bash
# Homebrew (권장)
brew install --cask stablyai/orca/orca

# 또는 DMG 다운로드
# https://github.com/stablyai/orca/releases/latest/download/orca-macos-arm64.dmg
```

### 2.2 Windows

```bash
# 설치 프로그램 다운로드
# https://github.com/stablyai/orca/releases/latest/download/orca-windows-setup.exe
```

### 2.3 Linux

```bash
# AppImage
# https://github.com/stablyai/orca/releases/latest/download/orca-linux.AppImage

chmod +x orca-linux.AppImage
./orca-linux.AppImage
```

### 2.4 모바일 앱

| 플랫폼 | 링크 |
|--------|------|
| iOS | [App Store](https://apps.apple.com/us/app/orca-ide/id6766130217) |
| iOS (TestFlight) | [TestFlight](https://testflight.apple.com/join/YjeGMQBA) |
| Android | [APK 다운로드](https://github.com/stablyai/orca/releases/download/mobile-android-v0.0.36/app-release.apk) |

### 2.5 첫 실행

1. **홈 디렉토리 접근 권한** 허용 (레포지토리 추가용)
2. `~/.claude`, `~/.codex` 등 기존 설정 자동 임포트
3. **Add Repo** 버튼으로 프로젝트 추가

---

## 3. 첫 3-에이전트 세션 시작하기

### 3.1 기본 플로우 (5분 컷)

```
1. Add Repo → 프로젝트 추가
2. + 버튼 → worktree 생성
3. 에이전트 선택 (Claude Code, Codex 등)
4. 동일한 태스크를 3개 worktree에 각각 전달
5. Split 화면으로 3개 에이전트 동시 관찰
6. 가장 좋은 결과 선택 → Diff 리뷰 → Commit & Push
```

### 3.2 상세 단계

**1단계: 레포지토리 추가**
- 좌측 사이드바 **Add Repo** 클릭
- 로컬 git 레포지토리 선택
- Orca가 base ref (`origin/main` 등) 자동 감지

**2단계: Worktree 생성**
- 레포지토리명 옆 **+** 버튼 클릭
- 태스크 이름 입력 (예: "fix-login-race")
- 기본 에이전트 사전 선택됨

**3단계: 에이전트 실행**
- 에이전트 콤보박스에서 Claude Code, Codex, Cursor 등 선택
- Orca가 자동으로 올바른 디렉토리에서 에이전트 CLI 실행

**4단계: 병렬 실행**
- 3개 worktree 생성
- 각각 다른 에이전트 할당
- 동일한 프롬프트 전달

**5단계: Split 화면**
- worktree 탭을 우측/하단으로 드래그하여 분할
- 3개 에이전트 동시 관찰

**6단계: 검토 & 배포**
- 각 worktree의 Diff 뷰어로 변경사항 비교
- **Annotate AI Diff**로 인라인 주석 추가
- 승인된 변경사항 Commit → Push

---

## 4. 에이전트 연결하기

### 4.1 지원되는 에이전트 목록

Orca는 **모든 CLI 에이전트**를 지원합니다. 다음은 기본 내장된 에이전트들:

| 에이전트 | 설정 방식 | 특징 |
|---------|----------|------|
| **Claude Code** | `~/.claude` 자동 임포트 | 사용량 추적, 계정 전환, hooks |
| **Codex (OpenAI)** | `~/.codex` 자동 임포트 | 사용량 추적, 계정 핫스왑 |
| **Grok (xAI)** | CLI 설치 후 자동 감지 | 자동 설정 |
| **Gemini (Google)** | CLI 설치 후 자동 감지 | 자동 설정 |
| **GitHub Copilot** | CLI 설치 후 자동 감지 | 자동 설정 |
| **OpenCode** | CLI 설치 후 자동 감지 | 상태 표시 |
| **Cursor CLI** | CLI 설치 후 자동 감지 | 딥 통합 |
| **Devin** | Devin CLI 설치 | 자동 설정 |
| **Droid (Factory)** | CLI 설치 | hooks, 상태 표시 |
| **Kimi (Moonshot)** | CLI 설치 | 자동 설정 |
| **Qwen Code** | `qwen` 실행파일 | 자동 설정 |
| **Charm Crush** | CLI 설치 | 자동 설정 |
| **Pi / OMP** | CLI 설치 | hooks, 상태 표시 |
| **기타 CLI** | 커스텀 등록 | 모든 CLI 에이전트 |

### 4.2 Claude Code 연결

Claude Code를 Orca에서 사용하는 방법:

```bash
# 1. Claude Code CLI 설치
npm install -g @anthropic-ai/claude-code

# 2. Claude Code 로그인
claude

# 3. Orca 실행 → 자동으로 ~/.claude 설정 임포트
```

**Claude Code 딥 통합 기능:**
- 사용량 추적 (토큰/비용)
- 계정 핫스왑 (Pro/Max 계정 전환)
- Agent Hooks & Memory
- --dangerously-skip-permissions 자동 적용

### 4.3 Codex (OpenAI) 연결

```bash
# 1. Codex CLI 설치
npm install -g @openai/codex

# 2. Orca 실행 → 자동 감지
```

**Codex 기능:**
- 사용량 추적
- 계정 핫스왑 (여러 API 키 전환)
- --dangerously-bypass-approvals 자동 적용

### 4.4 Grok (xAI) 연결

```bash
# 1. Grok CLI 설치
pip install grok-cli  # 또는 해당 설치법

# 2. Grok 로그인
grok auth

# 3. Orca 실행 → 자동 감지
```

### 4.5 Gemini (Google) 연결

```bash
# 1. Gemini CLI 설치
npm install -g @google/gemini-cli

# 2. Gemini 인증
gemini auth login

# 3. Orca 실행 → 자동 감지
```

### 4.6 GitHub Copilot CLI 연결

```bash
# 1. GitHub Copilot CLI 설치
npm install -g @githubnext/github-copilot-cli

# 2. GitHub 인증
github-copilot-cli auth

# 3. Orca 실행 → 자동 감지
```

### 4.7 OpenCode 연결

```bash
# 1. OpenCode CLI 설치
npm install -g opencode-ai

# 2. OpenCode 실행 (처음 한 번 /connect 실행)
opencode
/connect

# 3. Orca 실행 → 자동 감지
```

### 4.8 커스텀 CLI 에이전트 등록

목록에 없는 CLI 에이전트는 **Settings → Agents**에서 수동 등록:

```yaml
# orca.yaml (프로젝트 루트)
agents:
  my-custom-agent:
    name: "My Agent"
    command: "my-agent-cli"
    args: ["--dir", "{worktree}"]
    env:
      MY_API_KEY: "{env:MY_API_KEY}"
```

### 4.9 권한 모드 설정

Orca는 기본적으로 모든 에이전트에 **YOLO 모드** (권한 우회)를 적용합니다:

```
Settings → Agents → Agent Permissions
  → Yolo (권한 프롬프트 없음, 기본값)
  → Manual (각 에이전트의 자체 승인 흐름 유지)
```

이유: worktree는 일회용이므로, 격리된 환경에서는 실험적 명령어를 허용하는 것이 생산적입니다.

---

## 5. 핵심 기능

### 5.1 Worktree 시스템

모든 작업은 **격리된 git worktree**에서 실행:

- 각 worktree는 독립적인 브랜치와 파일 시스템
- 서로 간섭 없이 병렬 작업 가능
- 완료된 worktree는 원클릭 삭제 (브랜치도 함께 정리)

```bash
# Orca CLI로 worktree 생성
orca worktree create --name "feature-login" --agent claude
```

### 5.2 Split 화면

터미널, 에이전트, 브라우저, Diff를 자유롭게 분할:

- 탭을 드래그하여 좌/우/상/하 분할
- Ghostty 스타일 WebGL 렌더링 터미널
- 무한 분할 가능
- 재시작해도 스크롤백 유지

### 5.3 Design Mode

내장 Chromium 브라우저로 UI 요소를 클릭하면:
- HTML 구조 전송
- CSS 스타일 전송
- 크롭된 스크린샷 전송
→ 에이전트가 UI 버그를 실시간 수정

### 5.4 Diff 뷰어 & Annotate

- AI가 생성한 Diff를 라인별로 검토
- **Annotate AI Diff**: 인라인 마크다운 주석 추가
- 모든 주석을 모아서 에이전트에 다시 전송
- GitHub PR 검토와 통합

### 5.5 파일 편집기

- Monaco 에디터 (VS Code 기반)
- 자동 저장
- 파일을 에이전트 프롬프트로 드래그 앤 드롭
- 마크다운, 이미지, PDF, Mermaid 뷰어 내장

### 5.6 GitHub & Linear 통합

- PR 브라우징 및 리뷰
- 이슈 트래킹
- Linear 프로젝트 보드
- Jira 연동

### 5.7 SSH Worktree

원격 서버에서 에이전트 실행:
- 자동 재연결
- 포트 포워딩
- 패스프레이즈 캐싱
- 전체 파일 편집 및 git 지원

### 5.8 모바일 컴패니언

- iOS/Android 앱으로 에이전트 상태 모니터링
- 알림 수신
- 원격에서 follow-up 전송

---

## 6. 고급 설정

### 6.1 orca.yaml (프로젝트 설정)

프로젝트 루트에 `orca.yaml` 파일로 설정:

```yaml
# orca.yaml
worktree:
  baseRef: origin/main
  sharedDirectories:
    - node_modules
    - .next

agents:
  claude:
    command: claude
    env:
      ANTHROPIC_CACHE_ENABLED: "true"

hooks:
  post-create: |
    echo "Worktree created!"
    npm install
```

### 6.2 Settings Reference

Orca 설정 (Cmd+,):

| 카테고리 | 주요 설정 |
|---------|----------|
| **General** | Orca CLI 등록, 업데이트 채널, UI 줌 |
| **Appearance** | 테마, 폰트, 아이콘, 언어 (한국어 지원) |
| **Git** | Base ref, 커밋 서명, 브랜치 자동 이름 |
| **Terminal** | 폰트, 커서, Ghostty/Warp 테마 임포트 |
| **Agents** | 에이전트 활성화/비활성화, 권한 모드, 계정 관리 |
| **Browser** | 프로필, 줌, Design Mode, Devtools |
| **SSH** | SSH 대상, 패스프레이즈, 점프 호스트 |
| **Notifications** | 에이전트 완료 알림, PR 체크 실패 |
| **Shortcuts** | 키바인딩 전체 설정 |

### 6.3 Agent Hooks

`~/.orca/agent-hooks/` 에 후크 스크립트 배치:

```bash
# ~/.orca/agent-hooks/claude-pre-invoke.sh
# Claude Code 실행 전에 실행됨
echo "[Hook] Claude Code starting in $(pwd)"
```

후크 종류:
- `{agent}-pre-invoke` — 실행 전
- `{agent}-post-invoke` — 실행 후
- `{agent}-post-tool-use` — 도구 사용 후

### 6.4 키보드 단축키

| 단축키 | 기능 |
|--------|------|
| `Cmd+,'` | 설정 열기 |
| `Cmd+P` | Quick Open (파일/워크트리/명령어 검색) |
| `Cmd+N` | 새 워크트리 |
| `Cmd+W` | 탭 닫기 |
| `Cmd+Shift+]` | 다음 탭 |
| `Cmd+Shift+[` | 이전 탭 |
| `Ctrl+Tab` | 최근 탭 |
| `Cmd+Shift+A` | Diff 주석 추가 |
| `Cmd+Option+W` | 모든 에디터 탭 닫기 |

---

## 7. Worktree & 병렬 작업

### 7.1 고급 Worktree 전략

**레시피 1: 동일 태스크 3개 에이전트 레이스**

```
목표: 로그인 버그 수정
방법: Claude Code, Codex, Cursor에 동일 프롬프트 전달
      → 가장 좋은 결과 선택 → 나머지 삭제
```

**레시피 2: 파이프라인 분할**

```
1번 worktree: Claude Code로 백엔드 API 구현
2번 worktree: Codex로 프론트엔드 구현
3번 worktree: Qwen Code로 테스트 작성
→ 각자 완료 후 PR 생성
```

**레시피 3: 리뷰 파이프라인**

```
1번 worktree: 기능 구현 (Claude Code)
2번 worktree: 1번 결과 Diff 리뷰 → 주석
3번 worktree: 2번 주석 반영하여 수정
```

### 7.2 Worktree Checkpoints

```bash
# 현재 워크트리 상태 저장
orca snapshot create

# 저장된 상태로 복원
orca snapshot restore

# 변경사항 보기
orca snapshot diff
```

### 7.3 Worktree 공유 디렉토리

`node_modules`처럼 큰 디렉토리는 모든 worktree가 공유:

```yaml
# orca.yaml
worktree:
  sharedDirectories:
    - node_modules
    - .next
    - vendor/bundle
```

---

## 8. 오케스트레이션 (자동화)

### 8.1 핵심 개념

Orca 오케스트레이션은 다중 에이전트 워크플로우를 구조화:

| 개념 | 설명 |
|------|------|
| **Run** | 지속적인 네임스페이스 + 코디네이터 인박스 |
| **Task** | 작업 항목 (spec, 의존성, 상태) |
| **Dispatch** | 태스크를 터미널에 할당 |
| **Worker** | 태스크를 실행하는 에이전트 |
| **Message** | 인박스 메일 (worker_done, question 등) |
| **Decision Gate** | 태스크를 블로킹하는 결정 지점 |

### 8.2 기본 오케스트레이션 루프

```bash
# 1. Run 생성
orca orchestration run-create \
  --objective "Split checkout QA and summarize blockers"

# 2. Task 생성
orca orchestration task-create \
  --spec "Audit billing settings for mobile layout" \
  --task-title "Billing audit"

# 3. Worker 시작 (에이전트 할당)
orca orchestration worker-start \
  --task <taskId> \
  --worktree current \
  --agent codex

# 4. 완료 대기
orca orchestration check \
  --wait \
  --types worker_done,escalation,question \
  --timeout-ms 900000

# 5. 결과 확인 및 ACK
orca orchestration check --ack <deliveryId> \
  --wait --types worker_done
```

### 8.3 그룹 메시징

```bash
# 모든 에이전트에게 브로드캐스트
orca orchestration send --to @all \
  --subject "Heads up" \
  --body "Pausing dispatches for review."

# 특정 에이전트 타입에게만
orca orchestration send --to @claude \
  --subject "Claude agents only" \
  --body "Use Sonnet model for this task."
```

그룹 주소: `@all`, `@idle`, `@claude`, `@codex`, `@opencode`, `@gemini`, `@droid`, `@grok`, `@cursor`, `@worktree:<id>`

### 8.4 Worker 계약

Worker는 완료 시 반드시 `worker_done` 메시지 전송:

```bash
orca orchestration send \
  --type worker_done \
  --subject "Completed mobile audit" \
  --body "Fixed footer overlap; no follow-ups." \
  --task-id <taskId> \
  --dispatch-id <dispatchId> \
  --outcome succeeded \
  --files-modified "src/Billing.tsx"
```

### 8.5 Decision Gate

```bash
# Gate 생성 (태스크 블로킹)
orca orchestration gate-create \
  --task <taskId> \
  --question "Merge the changes?" \
  --options '["yes","no"]'

# Gate 해결
orca orchestration gate-resolve \
  --id <gateId> \
  --resolution "yes"
```

---

## 9. 기획 → 코드 → Git 워크플로우 자동화

### 9.1 완전 자동화 파이프라인

Orca의 오케스트레이션을 활용한 "기획 → 코딩 → Git" 파이프라인:

```bash
#!/bin/bash
# feature-pipeline.sh - 기획부터 PR까지 자동화

FEATURE_NAME=$1
FEATURE_DESC=$2

# 1. 기획: Plan worktree 생성 (읽기 전용 분석)
echo "=== Phase 1: Planning ==="
orca worktree create --name "plan-$FEATURE_NAME" --agent claude
orca terminal send \
  --worktree "plan-$FEATURE_NAME" \
  --message "Analyze the codebase and create an implementation plan for:
${FEATURE_DESC}
Output a detailed plan with files to modify, architecture decisions, and test strategy."

# 2. 기획 검토 완료 대기
echo "Waiting for plan..."
orca worktree wait --worktree "plan-$FEATURE_NAME" --for idle

# 3. 구현: 2개 에이전트가 각각 구현 (병렬)
echo "=== Phase 2: Implementation (Parallel) ==="

orca orchestration run-create \
  --objective "Implement $FEATURE_NAME"

# Task 1: 메인 구현
orca orchestration task-create \
  --task-title "Main implementation" \
  --spec "Implement $FEATURE_DESC. Follow the plan from plan-$FEATURE_NAME worktree."

# Task 2: 테스트
orca orchestration task-create \
  --task-title "Tests" \
  --spec "Write comprehensive tests for $FEATURE_NAME implementation."

# 2개 Worker 병렬 실행
orca orchestration worker-start \
  --task <mainTaskId> \
  --worktree new-child \
  --name "impl-$FEATURE_NAME" \
  --agent claude \
  --setup run

orca orchestration worker-start \
  --task <testTaskId> \
  --worktree new-child \
  --name "test-$FEATURE_NAME" \
  --agent codex \
  --setup run

# 4. 완료 대기
echo "=== Phase 3: Waiting for completion ==="
orca orchestration check --wait --types worker_done --timeout-ms 600000

# 5. Diff 검토
echo "=== Phase 4: Review ==="
orca worktree open --worktree "impl-$FEATURE_NAME"
# (수동 검토)

# 6. Commit & Push
echo "=== Phase 5: Ship ==="
orca worktree exec --worktree "impl-$FEATURE_NAME" \
  --command "git add -A && git commit -m 'feat: $FEATURE_NAME' && git push -u origin HEAD"

# 7. PR 생성
orca worktree exec --worktree "impl-$FEATURE_NAME" \
  --command "gh pr create --title 'feat: $FEATURE_NAME' --body '$FEATURE_DESC'"

# 8. 정리
orca worktree delete --worktree "plan-$FEATURE_NAME"
echo "Pipeline complete! PR created."
```

### 9.2 Quick Commands (프리셋)

자주 쓰는 워크플로우를 Quick Commands에 저장:

**Settings → Quick Commands**에서 추가:

```yaml
# 글로벌 Quick Command
commands:
  feature:
    label: "New Feature Pipeline"
    command: |
      orca worktree create --name "plan-$ARGUMENTS" --agent claude
      orca terminal send --worktree "plan-$ARGUMENTS" \
        --message "Create implementation plan for: $ARGUMENTS"
    scope: global

  ship:
    label: "Review & Ship"
    command: |
      orca snapshot diff
      echo "--- Review complete? Then: ---"
      echo "git add -A && git commit -m '$ARGUMENTS' && git push"
    scope: global

  review-pr:
    label: "Review PR"
    command: |
      orca worktree create --name "review-pr-$ARGUMENTS" --agent claude
      orca terminal send --worktree "review-pr-$ARGUMENTS" \
        --message "Review PR #$ARGUMENTS. Check code quality, bugs, security."
    scope: global
```

### 9.3 /orchestrate 명령어

Orca 내장 오케스트레이션 명령어로 태스크 위임:

```
/orchestrate 다음 작업을 분배해줘:
1. 백엔드 API 구현 (Claude Code)
2. 프론트엔드 컴포넌트 작성 (Codex)
3. 통합 테스트 작성 (Qwen Code)
각 작업은 독립된 worktree에서 실행하고,
완료되면 PR을 생성해줘.
```

### 9.4 3-에이전트 레이스 레시피

가장 좋은 구현을 찾기 위한 경쟁:

```
1. 동일한 기능 요청을 3개 에이전트에 전달
2. 각자 worktree에서 독립적으로 구현
3. 완료 후 Diff 비교
4. 가장 좋은 구현 선택
5. 나머지 worktree 삭제
6. 선택된 구현 Commit & PR
```

---

## 10. 팁 & 문제 해결

### 10.1 생산성 팁

| 팁 | 설명 |
|----|------|
| **레이스 모드** | 동일 태스크를 3개 에이전트에 전달 → 최고 결과 선택 |
| **Design Mode** | UI 버그 수정시 브라우저 요소 클릭 → 에이전트가 자동 수정 |
| **모바일 모니터링** | 자리 비울 때 모바일 앱으로 진행 상황 확인 |
| **SSH 오프로드** | 무거운 작업은 원격 서버에서 실행 |
| **Annotate AI Diff** | AI 생성 코드를 주석으로 리뷰하고 다시 에이전트에 전송 |
| **Quick Commands** | 자주 쓰는 워크플로우를 프리셋으로 저장 |
| **Split 화면** | 에이전트 + 터미널 + 브라우저 + Diff 동시에 보기 |

### 10.2 비용 절약 전략

- **ChatGPT Plus/Pro 구독** → Codex를 추가 비용 없이 사용
- **GitHub Copilot 구독** → Copilot CLI를 추가 비용 없이 사용
- **Claude Pro/Max 구독** → Claude Code 사용
- **무료 에이전트 활용:** OpenCode + 무료 LLM (Groq, DeepSeek 등)
- **YOLO 모드**로 권한 프롬프트 제거 → 시간 절약

### 10.3 일반적인 문제 해결

| 문제 | 해결 |
|------|------|
| 에이전트가 안 보임 | Settings → Agents에서 활성화 확인 |
| CLI 에이전트 미설치 | 각 에이전트 CLI를 먼저 설치 |
| 권한 오류 | Agent Permissions를 YOLO로 설정 |
| Git 오류 | GitHub API Budget 확인 (Settings → Git) |
| 업데이트 문제 | Shift+Click "Check for Updates"로 RC 채널 |
| 설정 초기화 | `~/.orca/settings.json` 삭제 후 재시작 |
| 언어 변경 | Settings → Appearance → Language (한국어 지원) |
| 자세한 정보 | [공식 문서](https://www.onorca.dev/docs) |

### 10.4 추천 설정 (한국어 사용자)

```yaml
# Settings → Appearance
theme: catppuccin-mocha
language: 한국어

# Settings → Terminal
shell: pwsh  # Windows

# Settings → Agents
agent permissions: Yolo
default agent: claude

# Settings → Git
base ref: origin/main
auto-rename branch: true

# Settings → General
updates: stable (Shift+click for RC)
```

---

## 부록: 참고 자료

| 자료 | 링크 |
|------|------|
| **공식 웹사이트** | https://www.onorca.dev |
| **문서** | https://www.onorca.dev/docs |
| **GitHub** | https://github.com/stablyai/orca |
| **디스코드** | https://discord.gg/fzjDKHxv8Q |
| **X (트위터)** | https://x.com/orca_build |
| **라이선스** | MIT (완전 무료) |
| **iOS 앱** | https://apps.apple.com/us/app/orca-ide/id6766130217 |

---

> **참고:** Orca는 완전 무료 오픈소스 도구(MIT 라이선스)입니다. 각 AI 에이전트의 사용료는 개별적으로 부담하며 (Claude Pro, ChatGPT Plus, GitHub Copilot 등 기존 구독 활용 가능), Orca 자체는 무료입니다.
> 최신 정보는 [공식 문서](https://www.onorca.dev/docs)를 참조하세요.
