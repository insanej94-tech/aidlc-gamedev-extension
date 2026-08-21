# Game Export Guide — Sharing Your Game With the World

## Overview

This document defines how the AI helps a non-developer user export and distribute their finished (or in-progress) game. The goal is zero-friction sharing — the user says "친구한테 보여주고 싶어" and the AI makes it happen.

**Core Principle**: Exporting should be as simple as pressing play. The AI handles all technical configuration, platform requirements, and distribution logistics.

---

## Export Targets (Priority Order)

### Priority 1: Web (HTML5) — Easiest to Share

**Why first**: No installation needed, works in any browser, shareable via link.

**User experience**:
```text
AI says: "게임을 웹으로 내보낼게요. 링크 하나면 누구나 플레이할 수 있어요!"
→ Export
→ Upload to itch.io (or similar)
→ "여기 링크예요: [URL] — 이걸 보내면 바로 플레이 가능해요!"
```

**Technical (AI handles silently)**:
- Export template: Godot HTML5 export template
- Compression: enabled
- Thread support: check browser compatibility
- Audio: ensure Web Audio API compatibility
- Touch input: enable for mobile browsers

### Priority 2: Windows (.exe) — For Local Play

**Why second**: Most users are on Windows, easy to send file directly.

**User experience**:
```text
AI says: "Windows용 실행 파일을 만들었어요!
'[game-name].exe' 파일을 친구한테 보내면 바로 실행돼요."
```

**Technical (AI handles silently)**:
- Export as .zip containing .exe and .pck
- Include all required DLLs
- Set proper icon (if custom art exists)
- Name the exe clearly (not "Godot_project.exe")

### Priority 3: macOS — If Requested

**Technical notes**: Requires code signing for easy distribution. Without it, users need to right-click → Open. AI explains this simply.

### Priority 4: Linux — If Requested

**Technical notes**: Export as .x86_64, straightforward.

### Priority 5: Mobile (Android/iOS) — Advanced

**Technical notes**: Requires additional setup (SDK, signing). Only do if explicitly requested.

---

## Export Process (AI Workflow)

### First-Time Setup

The first export requires one-time setup of export templates:

```text
AI says: "처음 내보내기를 하려면 한 가지 준비가 필요해요.
Godot에서:

1. 상단 메뉴 → Editor → Manage Export Templates
2. 'Download and Install' 클릭 (1-2분 걸려요)
3. 완료되면 말해주세요!

이건 처음 한 번만 하면 돼요."
```

### Subsequent Exports

After initial setup, AI handles everything:

1. Configure export preset (silently)
2. Build the game
3. Test the export (if possible)
4. Package for distribution
5. Present to user with sharing instructions

---

## Distribution Platforms

### itch.io (Recommended for Beginners)

**Why**: Free, easy, supports all platforms, built-in web player.

**Setup flow**:
```text
AI says: "itch.io에 게임을 올려볼까요? 무료 플랫폼이고, 링크만 보내면
누구나 바로 플레이할 수 있어요.

1. itch.io/register 에서 계정 만들기
2. 계정 만들면 말해주세요 — 나머지는 제가 가이드할게요!"
```

**After account creation**:
```text
"좋아요! 이제:
1. itch.io/game/new 접속
2. 제목: [게임 이름]
3. Kind of project: HTML (웹으로 올릴 거예요)
4. Upload: [내보낸 zip 파일 업로드]
5. 'This file will be played in the browser' 체크
6. Visibility: Draft (나중에 공개 가능)
7. Save

완료되면 링크가 생겨요!"
```

### GitHub Pages (Alternative for Web)

If user already has GitHub:
```text
AI handles:
1. Create gh-pages branch
2. Deploy HTML5 export
3. Enable GitHub Pages in repo settings
4. Present URL to user
```

### Direct File Sharing (Simplest)

```text
For Windows .exe:
- Zip the export folder
- Share via Google Drive, Dropbox, or direct send
- AI provides the zip file path

For quick testing with friends:
- "이 폴더를 zip으로 압축해서 보내면 돼요!"
```

---

## Export Quality Checklist (AI Verifies Automatically)

### Before Export

```text
□ Game runs without errors (F5 test passes)
□ All scenes load correctly
□ No placeholder text visible ("test", "debug", "TODO")
□ Main menu exists with start button
□ Game over screen exists with restart option
□ Window title is set (not "Godot Engine")
□ Window icon is set (if custom art exists)
□ Default window size is appropriate
□ Audio plays correctly
□ No debug output visible to player
```

### Platform-Specific Checks

```text
Web (HTML5):
□ Loading screen shows game name (not "Godot Engine")
□ Canvas size is appropriate for browsers
□ Touch input works (for mobile browsers)
□ Audio starts after first user interaction (browser policy)
□ File size is reasonable (< 50MB ideally)

Windows:
□ .exe has a proper name
□ Runs without Godot installed
□ All assets included in .pck
□ No console window appears (unless intended)

Mobile (if applicable):
□ Touch controls work
□ Portrait/landscape locked appropriately
□ App icon set
□ Splash screen configured
```

---

## User Communication

### When Game is Ready to Share

```text
AI says: "🎉 게임이 공유 준비 됐어요!

어떻게 공유하고 싶어요?

A) 웹 링크로 (가장 쉬움 — 링크만 보내면 됨)
B) 파일로 보내기 (Windows .exe)
C) 둘 다!
D) 아직 좀 더 만들고 싶어 (나중에)

언제든 '내보내기 해줘'라고 하면 바로 해드릴게요!"
```

### After Successful Export

```text
AI says: "✅ 내보내기 완료!

🌐 웹 버전: [URL]
💾 Windows 버전: [파일 경로]

공유할 때 이렇게 보내면 돼요:
'나 게임 만들었어! 여기서 해봐: [링크]'

피드백 받으면 말해줘요 — 바로 반영할 수 있어요!"
```

### If Export Fails

```text
AI says: "잠깐, 내보내기 중에 작은 문제가 있었어요. 고치는 중..."
→ Fix silently
→ "✅ 해결했어요! 다시 내보냈습니다."

If unfixable:
"내보내기에 문제가 있어서 다른 방법으로 할게요.
[대안 설명] — 결과는 같아요!"
```

---

## Version Management for Releases

### Versioning Strategy

```text
v0.1 — First playable (core mechanic works)
v0.2 — Second feature set (scoring, game over)
v0.3 — Third feature set (polish, effects)
...
v1.0 — "Complete" first version (all planned features)
v1.1+ — Updates based on feedback
```

### AI Manages Versions Automatically

- Tag each export with version number in Git
- Update GAME_STATUS.md with export history
- Keep previous versions accessible for comparison
- Commit message: `release: v[version] — [what's new in plain language]`

### Changelog for User

```text
GAME_STATUS.md에 자동 추가:

## 배포 이력
- v0.3 (2024-03-15) — 이펙트와 소리 추가, itch.io 공개
- v0.2 (2024-03-10) — 점수와 게임오버 추가
- v0.1 (2024-03-08) — 첫 플레이 가능 버전 (매칭 기본 동작)
```

---

## Post-Export: Gathering External Feedback

### When Friends Play

```text
AI says: "친구들이 플레이하면서 어떤 말 했는지 알려줘요!
뭐든 괜찮아요 — '재밌다', '어렵다', '버그 있다' 뭐든!

모아서 한번에 알려주셔도 되고, 생각날 때마다 하나씩 말해도 돼요."
```

### Feedback Categories AI Processes

| External Feedback | AI Action |
|---|---|
| "재밌어!" | Log as positive signal, continue current direction |
| "어려워" | Assess if difficulty adjustment needed |
| "버그 있어" | Ask for specifics, fix immediately |
| "이거 추가하면 좋겠다" | Add to backlog, discuss priority with user |
| "그래픽이 별로" | Suggest art upgrade path |
| "짧아" / "금방 끝나" | Discuss content expansion (more levels/modes) |

---

## Mobile Export (Advanced — Only If Requested)

### Android

```text
Requirements:
- Android SDK (AI guides installation)
- Debug keystore (AI creates)
- Export template for Android

AI says: "모바일로 내보내려면 좀 더 설정이 필요해요.
20분 정도 걸리는데, 한번 해두면 계속 쓸 수 있어요.
지금 할까요?"
```

### iOS

```text
AI says: "iOS(아이폰)로 내보내려면 macOS 컴퓨터와 Apple 개발자 계정이 필요해요.
(연간 $99 비용)

지금은 웹 버전으로 아이폰에서도 플레이할 수 있으니,
나중에 앱스토어에 올리고 싶을 때 다시 말해주세요!"
```
