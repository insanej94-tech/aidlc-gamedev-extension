# Asset Management Guide — Visual and Audio Resources

## Overview

This document defines how the AI manages game assets (images, sounds, music, fonts) for a non-developer user. The AI handles all technical aspects of asset integration while guiding the user through creative choices.

**Core Principle**: The user never needs to understand file formats, resolutions, import settings, or folder structures. They say "I want cute blocks" and the AI makes it happen.

---

## Asset Strategy: Layered Approach

### Layer 1: Instant Placeholders (AI Creates Immediately)

For rapid prototyping, the AI generates functional placeholders without asking:

- **Blocks/pieces**: Colored rectangles or circles with distinct colors
- **UI elements**: Simple shapes with text labels
- **Backgrounds**: Solid colors or simple gradients
- **Sound effects**: Silence or system beeps (marked for replacement)
- **Fonts**: Godot default font or bundled open-source font

These allow immediate playtesting. The user sees a working game, not placeholder art.

### Layer 2: Generated Assets (AI Creates Proactively)

As the game develops, the AI upgrades placeholders:

- **Simple sprites**: Using Godot's drawing API (rounded rects, circles with faces, patterns)
- **Particle effects**: Godot's built-in particle system
- **UI themes**: Godot's theme system with colors and styles matching chosen aesthetic
- **Procedural audio**: Simple synthesized sounds via AudioStreamGenerator

### Layer 3: External Assets (AI Suggests, User Approves)

When the game needs polish beyond what code can generate:

- Free asset packs from curated sources
- AI-generated images (if user has access to image generation tools)
- Music/SFX from free libraries

---

## Free Asset Sources (AI Reference)

### Sprites and Art

| Source | URL | Best For | License |
|---|---|---|---|
| Kenney | kenney.nl | Clean, consistent game art | CC0 (free, no attribution) |
| OpenGameArt | opengameart.org | Varied styles, large library | Various (check per asset) |
| itch.io Assets | itch.io/game-assets/free | Curated packs | Various |
| Pixel Frog | pixelfrog-assets.itch.io | Pixel art characters/tiles | Free |

### Sound Effects

| Source | URL | Best For | License |
|---|---|---|---|
| Freesound | freesound.org | Individual SFX | CC (attribution varies) |
| Kenney Audio | kenney.nl | UI sounds, impacts | CC0 |
| JSFXR | sfxr.me (web tool) | Retro/arcade SFX | Generated (free) |
| Pixabay Audio | pixabay.com/sound-effects | General SFX | Free commercial use |

### Music

| Source | URL | Best For | License |
|---|---|---|---|
| Kevin MacLeod | incompetech.com | BGM variety | CC-BY |
| Pixabay Music | pixabay.com/music | Loop-friendly tracks | Free commercial use |
| FreePD | freepd.com | Public domain BGM | CC0 |

### Fonts

| Source | URL | Best For | License |
|---|---|---|---|
| Google Fonts | fonts.google.com | Clean UI fonts | Open Font License |
| Font Squirrel | fontsquirrel.com | Game-friendly fonts | Various (free) |
| DaFont | dafont.com | Decorative/themed | Check per font |

---

## AI Behavior: Asset Integration Flow

### When a New Feature Needs Visuals

```text
1. Implement with placeholder immediately (don't wait for art)
2. Note what final art is needed in GAME_STATUS.md backlog
3. After feature is confirmed working via playtest, suggest art upgrade:

AI says: "블록 매칭이 잘 작동하네요! 지금은 색깔 네모로 되어 있는데,
나중에 예쁜 그림으로 바꿀 수 있어요. 지금 바꿔볼까요, 아니면 나중에?"
```

### When User Requests Visual Change

```text
User: "블록이 더 예뻤으면 좋겠어"

AI response:
1. Ask one design question:
   "어떤 느낌으로 할까요?
    A) 동글동글 캐릭터 (표정 있는)
    B) 반짝이는 보석
    C) 깔끔한 도형 (그라데이션)
    D) 다른 아이디어?"

2. On answer, find or create matching assets
3. Integrate and present for review
4. "이런 느낌이에요! 마음에 드세요?"
```

### When Using External Assets

```text
AI behavior:
1. Search for matching free assets
2. Verify license compatibility (prefer CC0 or CC-BY)
3. Download and integrate
4. Tell user what was added: "Kenney에서 귀여운 블록 세트를 가져왔어요 (무료, 상업적 사용 가능)"
5. If attribution required, add to credits file automatically
```

---

## Asset Pipeline (AI Internal)

### File Organization

```text
assets/
├── sprites/
│   ├── blocks/          # 블록/퍼즐 조각
│   ├── ui/              # 버튼, 패널, 아이콘
│   ├── effects/         # 이펙트 스프라이트시트
│   └── backgrounds/     # 배경 이미지
├── audio/
│   ├── sfx/             # 효과음 (.wav or .ogg)
│   ├── music/           # 배경음악 (.ogg)
│   └── ui/              # UI 사운드 (클릭, 호버)
├── fonts/               # 폰트 파일 (.ttf)
└── themes/              # Godot UI 테마 (.tres)
```

### Import Settings (AI Handles Silently)

- **Sprites**: Filter = Nearest (pixel art) or Linear (smooth), no mipmaps for 2D
- **Audio SFX**: Loop = false, format = WAV or OGG
- **Audio Music**: Loop = true, format = OGG (smaller file size)
- **Fonts**: Import as DynamicFont, set appropriate default size

### Sprite Sheet Handling

- Prefer sprite sheets over individual files for animated sprites
- Use Godot's AnimatedSprite2D or AnimationPlayer
- Standard sizes: 16x16, 32x32, 64x64 for blocks; larger for characters/effects

---

## Sound Design Principles (AI Applies Automatically)

### Every Action Gets a Sound

| Game Event | Sound Type | Feel |
|---|---|---|
| Block placed | Short thud/click | Satisfying, not loud |
| Match cleared | Bright chime/pop | Rewarding, scales with combo |
| Chain/combo | Ascending pitch sequence | Building excitement |
| Hard drop | Impact/slam | Powerful, brief |
| Game over | Descending tone/sad jingle | Not punishing, brief |
| Level up | Fanfare/celebration | Energetic, memorable |
| Menu click | Soft click/tap | Clean, responsive |
| Block rotation | Quick whoosh | Light, not distracting |

### Music Design

- **Gameplay**: Looping, non-distracting, builds tension subtly as speed increases
- **Menu**: Lighter version of gameplay theme or distinct calm track
- **Game Over**: Brief, then back to menu music
- **Volume**: Music at 50-60% by default, SFX at 80-100%

### Audio Implementation Pattern

```text
AudioManager (Autoload) responsibilities:
- Pool SFX players (avoid creating/destroying per sound)
- Manage music crossfades between scenes
- Respect user volume settings
- Pitch variation on repeated sounds (±10%) to avoid monotony
- Combo sounds: pitch increases with each chain level
```

---

## User-Facing Asset Communication

### When AI Adds Assets

```text
"✨ 사운드를 넣었어요:
 - 블록 놓을 때: 톡 하는 소리
 - 블록 터질 때: 팡! 하는 소리
 - 콤보: 점점 높아지는 소리

실행해서 들어보세요! 소리가 안 맞으면 말해주세요."
```

### When User Wants Custom Art

```text
"직접 그림을 쓰고 싶으면 이렇게 하면 돼요:
1. 원하는 그림을 준비 (PNG 파일)
2. assets/sprites/blocks/ 폴더에 넣기
3. 저한테 '그림 바꿔줘'라고 하면 끝!

크기는 제가 알아서 맞출게요."
```

### Credits Management

```text
AI automatically maintains:
- CREDITS.md in project root
- Lists all external assets used
- Includes license type and attribution
- User never needs to manage this
```

---

## AI Image Generation Integration (Optional)

If user has access to AI image generation tools:

```text
AI says: "혹시 AI 그림 생성기 (미드저니, DALL-E 등) 쓸 수 있어요?
있으면 게임에 딱 맞는 그림을 만들 수 있는데!"

If yes:
- AI provides optimized prompts for game assets
- Guides on size/format requirements
- Handles integration into the project

Prompt template for blocks:
"[style] puzzle block, [color], simple, clean edges, 
transparent background, 64x64 pixels, game asset, 
top-down view, consistent style"
```

---

## Asset Upgrade Path

### Phase 1 (Prototype): Programmer Art
- Colored shapes, default font, no sound
- Goal: Test gameplay, not visuals

### Phase 2 (Alpha): Styled Placeholders
- Godot-drawn shapes with theme colors
- Basic SFX from JSFXR
- Clean font from Google Fonts

### Phase 3 (Beta): Real Assets
- Downloaded or generated sprites
- Proper SFX and music
- Polished UI theme

### Phase 4 (Release): Final Polish
- Consistent art style across all elements
- Complete sound design
- Animations and juice effects fully tuned
- Credits file complete

The AI progresses through these phases naturally without requiring user action. Each phase transition is mentioned briefly: "비주얼을 좀 업그레이드했어요! 확인해보세요 ✨"
