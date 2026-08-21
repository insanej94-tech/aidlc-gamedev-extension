# Onboarding Guide — First-Time Setup

## Overview

This document guides the AI through helping a complete beginner set up their game development environment. The AI MUST follow this flow for new users who have never used Godot or AI-assisted development before.

**Trigger**: First session with a new user, or when no existing Godot project is detected in the workspace.

---

## Phase 1: Welcome and Orientation

### What to Say

```text
🎮 안녕하세요! 게임 만들기를 시작해볼까요?

제가 기술적인 건 다 알아서 할 테니까,
당신은 "어떤 게임을 만들고 싶은지"만 생각하시면 돼요.

먼저 게임 만들 준비를 같이 해볼게요.
5분이면 끝나요!
```

---

## Phase 2: Godot Installation Check

### AI Behavior

1. Check if Godot is already installed on the system
2. If installed: skip to Phase 3
3. If not installed: guide through installation

### Installation Guide (Present to User)

```text
📥 Godot 엔진을 설치할게요. 게임을 실행하고 테스트하는 도구예요.

1. 이 링크를 열어주세요: https://godotengine.org/download
2. "Godot Engine" (왼쪽 버튼)을 눌러서 다운로드
3. 다운로드된 zip 파일을 원하는 곳에 풀어주세요
4. 풀어진 폴더에서 "Godot" 파일을 실행

설치할 필요 없이 바로 실행되는 프로그램이에요!
끝나면 말해주세요 ✓
```

### Platform-Specific Notes (AI uses internally)

- **Windows**: Download the standard 64-bit version. No installer needed — extract zip and run .exe
- **macOS**: Download .dmg or .zip. May need to right-click → Open first time (Gatekeeper)
- **Linux**: Download .zip, make executable with chmod +x if needed

### Troubleshooting (AI handles silently)

- If user reports Gatekeeper warning on macOS → guide through System Preferences → Security
- If user reports missing DLLs on Windows → suggest .NET version or standard version
- If Godot opens but looks wrong → likely display scaling issue, guide through Editor → Editor Settings → Interface → Editor → Display Scale

---

## Phase 3: Project Creation

### AI Behavior

Once Godot is confirmed working, the AI creates the project structure automatically:

1. Create a new Godot project in the workspace
2. Set up the standard folder structure
3. Create `project.godot` with appropriate settings
4. Create `GAME_STATUS.md` for session continuity

### Standard Project Structure (AI creates silently)

```text
game-project/
├── project.godot              # Godot 프로젝트 설정
├── GAME_STATUS.md             # 진행 상황 기록 (AI가 관리)
├── scenes/                    # 게임 화면들
│   └── main.tscn
├── scripts/                   # 게임 동작 규칙들
│   └── main.gd
├── assets/                    # 그림, 소리 등
│   ├── sprites/
│   ├── audio/
│   └── fonts/
├── ui/                        # 메뉴, 버튼 등
└── resources/                 # 게임 데이터
```

### What to Tell the User

```text
✅ 프로젝트 준비 완료!

폴더가 만들어졌어요. Godot에서 이 폴더를 열면 됩니다:
1. Godot 실행
2. "Import" 버튼 클릭
3. [프로젝트 경로]의 "project.godot" 파일 선택
4. "Import & Edit" 클릭

열리면 말해주세요!
```

---

## Phase 4: First Run Test

### AI Behavior

Create a minimal "Hello World" scene that proves the project works:

- A simple colored rectangle or sprite on screen
- Text saying "게임 준비 완료!" 
- Runs without errors when F5 is pressed

### What to Tell the User

```text
🎯 한번 잘 되는지 테스트해볼게요!

Godot 화면 오른쪽 위에 ▶ (재생) 버튼을 누르거나 F5를 눌러보세요.

화면에 "게임 준비 완료!" 라고 뜨면 성공이에요!
```

### If It Works

```text
🎉 완벽해요! 이제 게임 만들 준비가 끝났어요.

그럼 이제 어떤 게임을 만들지 이야기해볼까요? 🎮
```

Then proceed to `game-design-interview.md`.

### If It Doesn't Work

- The AI troubleshoots silently (check project.godot, main scene assignment, etc.)
- Present simple instructions to the user if their action is needed
- Never show error logs directly

---

## Phase 5: IDE Connection (If Using Kiro/External Editor)

### For Kiro Users

```text
💡 참고: 지금 이 대화창에서 바로 게임을 만들 수 있어요.
제가 코드를 작성하고, Godot에서 실행 결과를 확인하는 방식이에요.

하실 일: Godot에서 결과 확인 (F5로 실행)
제가 할 일: 나머지 전부 😄
```

### What the User Needs to Know

- They will use TWO windows: this chat (for discussing/directing) and Godot (for playing/testing)
- They don't need to touch anything in Godot except the play button
- All code changes happen automatically through this chat

---

## Phase 6: GitHub Setup (Optional, Recommended)

### AI Behavior

If the user mentioned wanting to save on GitHub, or if this is detected as a goal:

1. Check if git is initialized
2. Check if GitHub remote is configured
3. If not, guide through minimal setup

### What to Tell the User

```text
💾 프로젝트를 GitHub에 저장해두면 작업이 날아가지 않아요.
설정해드릴까요? (한 번만 하면 돼요)
```

If user agrees:

```text
GitHub 계정이 있으세요?

A) 있어요
B) 없어요 (만들어야 해요)
C) 잘 모르겠어요
```

### GitHub Setup Flow

- **Has account**: Create repo, set remote, initial commit — all done by AI
- **No account**: Guide to github.com signup (minimal steps), then proceed
- **Unsure**: Help them check, then proceed accordingly

### After Setup

```text
✅ GitHub 연결 완료!

이제부터 중요한 진전이 있을 때마다 자동으로 저장돼요.
혹시 이전 버전으로 돌아가고 싶으면 "아까 버전으로 돌려줘"라고만 하면 됩니다.
```

---

## Onboarding Completion Checklist (AI Internal)

Before proceeding to game design, verify:

- Godot is installed and launches correctly
- Project is created and opens in Godot
- Test scene runs without errors
- User understands the two-window workflow (chat + Godot)
- Git is initialized (optional but recommended)
- GitHub remote is configured (if user wants)
- `GAME_STATUS.md` exists with initial state
- User is ready and excited to start designing their game
