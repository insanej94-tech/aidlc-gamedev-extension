# Localization Guide — Multi-Language Support

## Overview

This document defines how the AI implements multi-language support in the game. The default language is Korean, but the structure should allow easy addition of other languages when needed.

**Core Principle**: Localization-ready architecture from day one, actual translations added only when requested. Never hardcode display strings.

---

## When to Apply This Guide

**Phase 1 (Architecture — Always)**:
- Separate all display text from code (even in Korean-only games)
- Use a string key system from the start
- Design UI to accommodate different text lengths

**Phase 2 (Translation — On Request)**:
- Only when user says "영어도 지원하고 싶어" or similar
- Or when preparing for international distribution

---

## Architecture: String Management

### Godot Translation System

```text
Project structure:
assets/
└── translations/
    ├── ko.csv          # Korean (default)
    ├── en.csv          # English (when added)
    ├── ja.csv          # Japanese (when added)
    └── translation.tres # Godot translation resource

CSV format:
key,ko,en,ja
MENU_START,시작,Start,スタート
MENU_SETTINGS,설정,Settings,設定
GAME_SCORE,점수,Score,スコア
GAME_OVER,게임 오버,Game Over,ゲームオーバー
GAME_RETRY,다시하기,Retry,リトライ
...
```

### String Key Naming Convention

```text
Format: CATEGORY_DESCRIPTION

Categories:
MENU_     — Menu items and navigation
GAME_     — In-game UI elements
TUTORIAL_ — Tutorial text and hints
SETTINGS_ — Settings labels
DIALOG_   — Any dialog/popup text
ERROR_    — Error messages (if shown to player)
ACHIEVE_  — Achievement names/descriptions

Examples:
MENU_START          → "시작"
MENU_QUIT           → "나가기"
GAME_SCORE          → "점수"
GAME_COMBO          → "콤보"
GAME_LEVEL          → "레벨"
GAME_NEXT           → "다음"
GAME_PAUSE          → "일시정지"
GAME_OVER_TITLE     → "게임 오버"
GAME_OVER_SCORE     → "최종 점수: {score}"
GAME_OVER_BEST      → "최고 기록: {best}"
GAME_OVER_RETRY     → "다시하기"
GAME_OVER_MENU      → "메뉴로"
TUTORIAL_MOVE       → "← → 로 이동"
TUTORIAL_ROTATE     → "↑ 로 회전"
TUTORIAL_DROP       → "스페이스 로 떨어뜨리기"
SETTINGS_VOLUME     → "음량"
SETTINGS_SPEED      → "속도"
SETTINGS_COLORBLIND → "색약 모드"
```

### Code Usage Pattern

```text
In GDScript (AI implements automatically):

# Instead of:
label.text = "점수: " + str(score)

# Use:
label.text = tr("GAME_SCORE") + ": " + str(score)

# Or with format:
label.text = tr("GAME_OVER_SCORE").format({"score": score})
```

---

## Design Considerations for Localization

### Text Length Variation

Different languages have vastly different text lengths:

```text
Korean:     "시작"        (2 chars)
English:    "Start"       (5 chars)
German:     "Starten"     (7 chars)
Japanese:   "スタート"    (4 chars)
Portuguese: "Começar"     (7 chars)

Rule: Design UI buttons and labels with 50% extra space
      OR use auto-sizing text that shrinks to fit
```

### AI Implementation

```text
For UI elements:
- Use Godot's Label autowrap and minimum size
- Buttons should use HBoxContainer with stretch
- Never hardcode pixel widths for text elements
- Test with longest expected translation (usually German or Portuguese)
```

### Font Considerations

```text
When supporting multiple scripts:
- Korean: needs Korean-supporting font (most Google Fonts support it)
- Japanese: needs CJK font (shared with Korean usually)
- Latin languages: any standard font works
- Arabic/Hebrew: needs RTL (right-to-left) support

Recommended approach:
- Use a single CJK-compatible font (e.g., Noto Sans CJK)
- Covers Korean, Japanese, Chinese, and Latin characters
- Fallback font configured in Godot for missing glyphs
```

---

## Translation Workflow

### When User Requests a New Language

```text
AI says: "영어 지원 추가할게요!

제가 할 일:
1. 게임에 쓰이는 모든 텍스트를 정리
2. 번역 파일 구조 만들기
3. 기본 번역 제공 (AI 번역 — 나중에 수정 가능)
4. 언어 선택 메뉴 추가

5분이면 돼요!"
```

### Translation Process

```text
Step 1: Extract all strings (AI does automatically)
        → List all tr() calls in the project
        → Generate CSV with Korean column filled

Step 2: Translate (AI provides first draft)
        → AI translates Korean → target language
        → Mark as "AI translated — review recommended"

Step 3: Integration (AI does automatically)
        → Add CSV to translation resources
        → Configure Godot project locale settings
        → Add language selector to settings menu

Step 4: Testing (AI + User)
        → Verify all strings appear correctly
        → Check no text overflow in UI
        → Verify special characters render properly
```

### Language Selector UI

```text
In settings menu:

🌐 언어 / Language
├── 한국어 (기본)
├── English
├── 日本語
└── [추가 가능]

Change takes effect immediately (no restart needed).
Save preference to user settings.
```

---

## What NOT to Localize

```text
Don't translate:
- Game title/brand name (unless specifically requested)
- Sound effects (language-independent)
- Musical elements
- Visual symbols (✓, ✗, ★, etc.)
- Numbers (use universal arabic numerals)
- Programming/file identifiers

Do localize:
- All menu text
- In-game UI labels
- Tutorial text
- Settings labels
- Achievement/unlock names
- Game over screen text
- Any text shown to the player
```

---

## Number and Date Formatting

```text
Score display:
- Korean: 1,234,567 (comma separator)
- English: 1,234,567 (same)
- German: 1.234.567 (dot separator)

AI implements:
- Use Godot's locale-aware formatting
- Or standardize on comma-separator (most common in games)
- For puzzle games, simple numbers are usually fine globally
```

---

## RTL (Right-to-Left) Considerations

```text
Only needed for: Arabic, Hebrew, Persian, Urdu

If requested:
- Godot 4 has built-in BiDi (bidirectional) text support
- UI layout needs to be mirrored
- This is complex — only implement if explicitly requested

AI says (if asked): "아랍어/히브리어 지원은 화면 레이아웃을 거울처럼 뒤집어야 해서
좀 더 복잡해요. 나중에 따로 작업하는 게 좋을 것 같아요.
지금은 영어/일본어 같은 좌-우 언어부터 추가할까요?"
```

---

## Localization Testing

### AI Verifies

```text
Automated checks:
□ All tr() keys have translations in all enabled languages
□ No hardcoded strings in scene files (.tscn)
□ No hardcoded strings in scripts (.gd) — only tr() calls
□ Font supports all characters in enabled languages
□ UI doesn't overflow with longest translations
□ Language switch works without restart
□ Preference persists across sessions
```

### Pseudo-Localization (AI Internal Test)

```text
Before requesting real translations, AI can test with:
- Doubled strings: "Start" → "Staarrtt" (tests overflow)
- Accented chars: "Start" → "Stàrt" (tests character support)
- Right-padding: "Start    " → (tests layout flexibility)

This catches UI issues before real translations are added.
```

---

## AI Behavior Summary

### From Day One (Always)

- Use `tr()` for every display string
- Store strings in CSV translation file
- Design UI with flexible text sizing
- Use CJK-compatible font

### When User Requests Translation

- Generate translation file for requested language
- Provide AI-translated first draft
- Add language selector
- Test all strings render correctly
- Note which strings may need human review

### What to Tell the User

```text
AI says (if topic comes up): "게임 텍스트는 처음부터 번역하기 쉬운 구조로 만들어뒀어요.
나중에 '영어도 추가해줘'라고 하면 바로 할 수 있어요!

지금은 한국어에 집중하고, 공유할 때 필요하면 추가하면 돼요."
```
