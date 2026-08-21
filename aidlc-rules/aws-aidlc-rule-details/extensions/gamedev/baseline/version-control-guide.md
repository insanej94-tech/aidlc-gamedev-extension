# Version Control Guide — Invisible Git for Non-Developers

## Overview

This document defines how the AI manages Git and GitHub for a non-developer user. The user should never need to understand version control — it should feel like "automatic saving" with the ability to go back in time.

**Core Principle**: Git is infrastructure, not a user-facing feature. The user experiences "save points" and "undo," not "commits" and "reverts."

---

## Automatic Commit Strategy

### When to Commit

| Event | Commit? | Message Format |
|---|---|---|
| Milestone completed | Yes | `feat: [마일스톤 이름]` |
| Bug fix after user report | Yes | `fix: [무엇을 고쳤는지]` |
| Parameter tuning (speed, size, etc.) | Yes (batch) | `fix: 게임 느낌 조정 ([구체적 변경])` |
| Design decision recorded | Yes | `docs: [결정 내용] 기록` |
| Session end | Yes (if uncommitted changes) | `chore: 세션 종료 저장` |
| Before risky change | Yes | `chore: [다음 작업] 전 저장 포인트` |
| GAME_STATUS.md update | Bundle with related commit | (included in parent commit) |

### Commit Message Convention

All commit messages follow conventional commits format (required by project CI) but content is in Korean for the user's `GAME_STATUS.md` reference:

```text
feat: 블록 매칭 시스템 완성 (M5)
fix: 블록 떨어지는 속도 조정 — 사용자 피드백 반영
docs: 게임 컨셉 확정 — 낙하형 퍼즐 + 중력 변환
chore: 세션 종료 저장
```

### Commit Frequency

- Minimum: every completed milestone
- Maximum: don't commit every tiny change (batch small fixes)
- Rule of thumb: if the user could say "아까로 돌아가줘" and mean this point, it should be a commit

---

## Branch Strategy

### Keep It Simple

For a single non-developer user, branches add unnecessary complexity:

- **Main branch only** for most work (`main`)
- Create a branch ONLY if:
  - Experimenting with a risky/uncertain feature the user might reject
  - The AI wants to try two different approaches for user comparison

### If Branching Is Needed

```text
Branch naming: experiment/[feature-name]
Example: experiment/gravity-flip

After user decides:
- Accepted → merge to main silently
- Rejected → delete branch silently, inform user "원래대로 돌렸어요"
```

The user never knows branches exist.

---

## GitHub Push Strategy

### When to Push

| Trigger | Push? |
|---|---|
| Milestone completed | Yes |
| End of session | Yes (always) |
| Before risky experiment | Yes (safety backup) |
| User explicitly says "저장해줘" | Yes |
| After every single commit | No (too frequent) |

### Push Failures

If push fails (network, auth, etc.):

- Retry silently once
- If still fails, inform user casually: "GitHub 저장은 나중에 할게요. 로컬에는 안전하게 저장돼 있어요!"
- Retry on next milestone completion

---

## Rollback Handling

### User Language → Git Action

| User Says | Git Action |
|---|---|
| "아까로 돌아가줘" | `git log` → find last milestone commit → `git revert` or `git reset` |
| "아까 버전이 더 좋았어" | Same as above |
| "이거 안 할래, 원래대로" | Revert to before current feature started |
| "어제 버전으로" | Find commits from previous session |
| "처음부터 다시" | Reset to first playable milestone (not empty project) |
| "[기능] 넣기 전으로" | Find commit before that feature was added |

### Rollback Protocol

1. Identify the target state from Git history
2. Assess what will be lost (list features/changes since that point)
3. Inform user in plain language:

```text
"그 시점으로 돌아가면 [이후에 추가된 것들]이 사라져요.
괜찮으세요? 아니면 [특정 부분만] 되돌릴 수도 있어요."
```

4. On confirmation, execute rollback
5. Verify game still runs
6. Confirm to user: "✅ 돌아갔어요! F5로 확인해보세요."

### Safety Rails

- Never hard reset without a backup branch/tag
- Before any destructive operation, create a safety tag: `backup/before-rollback-[date]`
- If user changes their mind after rollback: "다시 원래대로도 가능해요!" (cherry-pick or reset to backup)

---

## .gitignore Configuration

### Auto-Generated .gitignore for Godot Projects

```text
# Godot-specific ignores
.godot/
*.import
export_presets.cfg

# OS files
.DS_Store
Thumbs.db

# IDE
.vscode/
*.swp
*.swo

# Build outputs
build/
export/
```

The AI creates this automatically during project setup. User never sees it.

---

## GAME_STATUS.md — Session Continuity File

### Purpose

This file serves as the human-readable "save file" for the project state. It's committed with every milestone and serves as the AI's memory across sessions.

### Template

```markdown
# 🎮 게임 프로젝트 현황

## 게임 컨셉
- **장르**: [낙하형 퍼즐 / 매칭형 / 로그라이크 등]
- **핵심 메카닉**: [한 문장]
- **차별점**: [유니크한 요소]
- **분위기**: [비주얼 방향]
- **모드**: [싱글/대전/둘 다]

## 현재 진행 상황
- **현재 Phase**: [Phase 1/2/3/4]
- **마지막 완성 마일스톤**: M[번호] - [이름]
- **다음 마일스톤**: M[번호] - [이름]
- **전체 진행률**: [██████░░░░ 60%]

## 완성된 마일스톤
- [x] M1. [이름] (날짜)
- [x] M2. [이름] (날짜)
- [x] M3. [이름] (날짜)
- [ ] M4. [이름] ← 다음
- [ ] M5. [이름]

## 핵심 디자인 결정
- [날짜] 매칭 기준: 3개 연결 ("4개는 너무 어렵다")
- [날짜] 블록 떨어지는 속도: 초당 2칸 ("딱 좋아")
- [날짜] 비주얼 스타일: 파스텔 + 동글동글

## 플레이테스트 메모
- [날짜] "연쇄 터질 때 기분 좋다" → 연쇄 이펙트 강화
- [날짜] "좀 느리다" → 속도 30% 증가

## 나중에 추가할 것 (백로그)
- [ ] 대전 모드
- [ ] 특수 블록 (폭탄, 무지개)
- [ ] 배경음악
- [ ] 캐릭터 선택

## 알려진 문제
- (현재 없음)

## 기술 메모 (AI 참고용)
- Godot 버전: 4.x
- 해상도: [설정값]
- 주요 씬 구조: [간단 메모]
```

### Update Rules

- Update after every milestone completion
- Update after design decisions are made
- Update at session end
- Keep concise — this is a reference, not a diary
- User can read this file but doesn't need to

---

## GitHub Repository Setup

### Initial Setup (Done Once by AI)

1. Initialize git: `git init`
2. Create `.gitignore`
3. Initial commit: `feat: 프로젝트 초기 설정`
4. Create GitHub repo (if user has authorized)
5. Set remote and push
6. Confirm to user: "GitHub에 프로젝트가 만들어졌어요! [링크]"

### Repository Naming

- Suggest based on game concept: `puzzle-[theme]`, `[game-name]`
- If no game name yet: `my-puzzle-game` (can rename later)
- Always private by default (user can make public later if they want)

### README.md for the Game Repo

Auto-generated, simple:

```markdown
# [게임 이름 / 작업명]

[한 줄 게임 설명]

## 개발 도구
- Godot Engine 4.x
- AI-DLC 게임 개발 워크플로우

## 실행 방법
1. Godot Engine 다운로드: https://godotengine.org/download
2. 이 프로젝트를 Godot에서 열기
3. F5로 실행

## 현재 상태
[GAME_STATUS.md 참조]
```

---

## Multi-Session Continuity

### What Gets Preserved Across Sessions

- All code and game assets (Git)
- Game design decisions (GAME_STATUS.md)
- Milestone progress (GAME_STATUS.md)
- Playtest feedback history (GAME_STATUS.md)
- Backlog items (GAME_STATUS.md)

### What the AI Does at Session Start

1. `git status` — check for any unexpected changes
2. Read `GAME_STATUS.md` — recall project state
3. Present brief summary to user
4. Suggest next action

### If User Has Multiple Devices

- Everything is on GitHub — they can continue from any machine
- AI detects if local is behind remote and pulls automatically
- If conflicts exist (unlikely for single user), resolve silently
