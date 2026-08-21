# Accessibility Guide — Inclusive Game Design

## Overview

This document defines accessibility requirements that the AI applies to every feature by default. Accessibility is not an add-on — it's built into the foundation of every design decision.

**Core Principle**: Every player should be able to enjoy the game regardless of visual, auditory, motor, or cognitive differences. The AI implements accessibility features automatically without requiring the user to request them.

---

## Rule GAMEDEV-A1: Color Independence

**Rule**: Game information MUST never be communicated through color alone. Every color distinction must have a secondary indicator.

### Implementation

| Element | Color Only (Wrong) | Accessible (Correct) |
|---|---|---|
| Block types | Red, Blue, Green, Yellow | Red+Circle, Blue+Diamond, Green+Star, Yellow+Triangle |
| Danger state | Red border | Red border + pulse animation + icon |
| Match highlight | Color glow | Color glow + shape outline + particle |
| Player identity | Blue vs Red | Blue+Circle vs Red+Square |
| Health/damage | Color bar | Color bar + number + icon change |

### Color-Blind Mode Implementation

```text
Standard mode: Colors with subtle shape hints
Color-blind mode: High-contrast colors + prominent shape/pattern overlays

Deuteranopia (red-green): Replace red/green with blue/orange
Protanopia (red-weak): Replace reds with yellows/blues
Tritanopia (blue-yellow): Replace blue/yellow with red/green

AI implementation:
- Default: shapes + colors combined (works for most)
- Settings menu: "색약 모드" toggle with preview
- When enabled: swap color palette to high-contrast alternative
- Shapes/symbols remain the same (always visible)
```

### Pattern Overlays for Blocks

```text
Block visual design (always present, independent of color):

Type 1: ● (solid circle)
Type 2: ◆ (diamond)
Type 3: ★ (star)
Type 4: ▲ (triangle)
Type 5: ■ (square)
Type 6: ♥ (heart)

In color-blind mode: patterns become larger and more prominent
In standard mode: patterns are subtle but visible on close inspection
```

---

## Rule GAMEDEV-A2: Input Flexibility

**Rule**: The game MUST support multiple input methods and allow remapping of controls.

### Supported Input Methods

```text
Priority 1 (Always implement):
- Keyboard (full game playable with keyboard alone)
- Mouse/touch (full game playable with mouse/touch alone)

Priority 2 (Implement if applicable):
- Gamepad/controller support
- Touch gestures (mobile/tablet)

Priority 3 (Nice to have):
- One-handed mode (all actions on one side of keyboard)
- Switch/adaptive controller support
```

### Key Remapping

```text
AI implements:
- Settings menu with control remapping
- Show current bindings clearly
- Allow any key to be reassigned
- Prevent conflicting assignments (warn user)
- Save preferences to user file

Default alternatives always available:
- Arrow keys OR WASD for movement
- Space OR Enter for confirm
- Escape OR Backspace for cancel/pause
- Z/X OR Q/E for rotate/special
```

### Input Timing Accommodation

```text
Configurable timing:
- DAS (Delayed Auto Shift): adjustable 50-300ms
- Auto-repeat speed: adjustable
- Hard drop confirmation: optional (prevent accidental drops)
- Input buffer window: adjustable (how long a press "counts")

For players with motor difficulties:
- "Assisted mode" that slows the game when input is detected
- Longer windows for time-critical actions
- Option to disable time pressure entirely (puzzle mode)
```

---

## Rule GAMEDEV-A3: Visual Accessibility

**Rule**: All visual information must be perceivable by players with low vision or visual processing differences.

### Text Readability

```text
Requirements:
- Minimum font size: 16px (scalable up to 32px in settings)
- High contrast between text and background (4.5:1 ratio minimum)
- Sans-serif font for UI text (better readability)
- No important information conveyed only through thin/small text
- Score and critical info always prominently displayed
```

### Screen Clarity

```text
Requirements:
- Clear visual hierarchy (important info is biggest/brightest)
- Sufficient contrast between game elements and background
- No flickering/strobing effects (epilepsy risk)
- Screen shake intensity adjustable (including OFF option)
- Particle density adjustable
- Option to reduce visual complexity ("간결한 화면 모드")
```

### Reduced Motion Mode

```text
When enabled:
- Screen shake disabled
- Particles reduced to minimal
- Animations shortened (instant transitions)
- Flashing effects replaced with solid highlights
- Background animations stopped
- Camera zoom effects disabled

This helps:
- Players with motion sensitivity
- Players with epilepsy triggers
- Players who find effects distracting
```

---

## Rule GAMEDEV-A4: Audio Accessibility

**Rule**: No critical game information may be conveyed through audio alone. All audio cues must have visual equivalents.

### Visual Equivalents for Audio Cues

| Audio Cue | Visual Equivalent |
|---|---|
| Block landing sound | Brief flash/bounce on landing |
| Match sound | Particle burst at match location |
| Danger alarm | Screen edge pulsing red |
| Combo escalation | Increasing glow intensity |
| Game over tone | Screen darken + text overlay |
| Timer warning | Flashing timer display |
| Opponent attack incoming | Visual indicator on board edge |

### Volume Controls

```text
Separate volume sliders:
- Master volume (0-100%)
- Music volume (0-100%)
- SFX volume (0-100%)
- UI sounds (0-100%)

Special options:
- Mute all (quick toggle)
- Mono audio (for single-ear hearing)
- Audio cue descriptions (text popup describing what sounds mean)
```

### Subtitles/Captions (If Game Has Story/Dialog)

```text
- Always on by default
- Adjustable size
- Speaker identification
- Sound effect descriptions in brackets [블록 터지는 소리]
```

---

## Rule GAMEDEV-A5: Cognitive Accessibility

**Rule**: The game must be playable by people with varying cognitive abilities and attention spans.

### Clarity of Game State

```text
At any moment, the player should be able to answer:
- What am I supposed to do? (goal visible)
- How am I doing? (score/progress visible)
- What happens next? (next piece/turn visible)
- Am I in danger? (clear warning system)

Implementation:
- Persistent HUD with score, level, next piece
- Clear game-over warning (progressive, not sudden)
- Current objective visible if applicable
- No hidden information critical to basic play
```

### Pacing Options

```text
Speed settings:
- "여유롭게" — 50% normal speed, more time to think
- "보통" — standard speed
- "빠르게" — 150% speed for experienced players
- "시간 무제한" — no time pressure at all (turn-based feel)

Pause:
- Pause available at any time
- No penalty for pausing
- Game state preserved exactly
- Clear "paused" indicator
```

### Memory Assistance

```text
For games with complex state:
- Visual reminder of current rules/modifiers
- History of recent actions visible
- "What just happened" indicator for chain reactions
- Persistent display of key information (don't require memorization)
```

---

## Rule GAMEDEV-A6: Difficulty Accessibility

**Rule**: Every player should be able to experience the game's content regardless of skill level.

### Assist Features

```text
Optional assists (toggleable in settings):
- Slow motion (game runs at reduced speed)
- Extended time (more time for decisions)
- Reduced pressure (board clears slowly when nearly full)
- Hint system (highlights good moves)
- Auto-save before dangerous moments (easy retry)
- Invincibility mode (can't lose, just practice)

These NEVER affect high score tracking.
Separate "assisted" leaderboard if applicable.
```

### Difficulty Communication

```text
Present difficulty options without judgment:

"게임 속도:
🐢 여유롭게 — 천천히, 생각할 시간 충분
🐇 보통 — 적당한 긴장감
🚀 도전! — 빠르고 긴장감 있게
⏸️ 시간 무제한 — 순수하게 퍼즐만"

Never say "easy mode" (implies weakness).
Use positive framing: "relaxed," "challenging," "competitive."
```

---

## Settings Menu Structure

### AI Implements This Settings Menu Automatically

```text
⚙️ 설정

🎮 게임
├── 속도: [느긋 / 보통 / 빠름 / 무제한]
├── 난이도: [도움 있음 / 보통 / 도전]
└── 힌트: [켜기 / 끄기]

🎨 화면
├── 색약 모드: [끄기 / 적녹 / 청황]
├── 모션 줄이기: [끄기 / 켜기]
├── 화면 흔들림: [끄기 / 약하게 / 보통 / 강하게]
├── 글자 크기: [보통 / 크게 / 아주 크게]
└── 간결한 화면: [끄기 / 켜기]

🔊 소리
├── 전체 음량: [━━━━━━━━━━] 80%
├── 음악: [━━━━━━━━━━] 60%
├── 효과음: [━━━━━━━━━━] 100%
└── 모노 오디오: [끄기 / 켜기]

🎮 조작
├── 키 설정: [변경]
├── 입력 감도: [낮음 / 보통 / 높음]
└── 한 손 모드: [끄기 / 왼손 / 오른손]
```

---

## AI Implementation Priority

### Phase 1 (Build Into Every Feature From Start)

- Color-independent design (shapes + colors)
- Keyboard fully playable
- Text readable (size, contrast)
- No audio-only information
- Pause always available

### Phase 2 (Add During Polish)

- Color-blind mode toggle in settings
- Volume controls (separate sliders)
- Screen shake adjustable
- Speed options
- Reduced motion mode

### Phase 3 (Add Before Public Release)

- Full key remapping
- One-handed mode
- Font size options
- Assist/accessibility mode
- Complete settings menu

---

## AI Behavior Rules

### Default Behavior

The AI implements Phase 1 accessibility in every feature automatically. The user is never asked about accessibility — it's built in by default.

### When User Mentions Accessibility

```text
AI says: "접근성은 기본으로 넣어뒀어요!
- 색깔만으로 구분하지 않게 모양도 넣었고
- 키보드만으로도 전부 할 수 있고
- 속도 조절도 가능해요

추가로 원하는 거 있으면 말해주세요!"
```

### When External Tester Reports Accessibility Issue

```text
Treat as high priority bug:
1. Fix immediately
2. Check if same issue exists elsewhere in the game
3. Add to Phase 2/3 checklist if systemic
4. Thank the user for reporting
```

### Testing Accessibility

```text
AI self-tests:
- Play the game with color information removed (greyscale mental model)
- Verify every action is possible with keyboard only
- Check all text at minimum size is readable
- Confirm no audio-only critical information
- Ensure game is playable at slowest speed setting
```
