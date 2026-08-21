# Godot Puzzle Game Implementation Patterns

## Overview

This document is an AI-internal reference for implementing puzzle games in Godot 4.x. The user never sees this content. It provides proven architectural patterns, code structures, and best practices specific to puzzle game development.

**Usage**: The AI consults this document when implementing features. It does NOT share these technical details with the user.

---

## Architecture Patterns

### Scene Tree Structure (Standard Puzzle Game)

```text
Main (Node2D)
├── GameBoard (Node2D)
│   ├── Grid (TileMap or Node2D with grid logic)
│   ├── BlockContainer (Node2D — holds active blocks)
│   ├── EffectsLayer (Node2D — particles, animations)
│   └── GhostPreview (Node2D — shows where block will land)
├── UI (CanvasLayer)
│   ├── ScoreDisplay (Label/RichTextLabel)
│   ├── ComboCounter (Label with animation)
│   ├── NextBlockPreview (TextureRect/Node2D)
│   └── GameOverPanel (Panel — hidden by default)
├── AudioManager (Node — Autoload singleton)
├── GameState (Node — Autoload singleton)
└── InputHandler (Node — processes and routes input)
```

### Autoload Singletons

Always create these as autoloads for puzzle games:

- **GameState**: Current score, level, game phase, settings
- **AudioManager**: SFX and music with pooling
- **EventBus**: Global signal bus for decoupled communication
- **SaveManager**: Persistent data (high scores, unlocks, settings)

### Signal Architecture

Prefer signals over direct references for loose coupling:

```text
EventBus signals for puzzle games:
- block_placed(position, block_type)
- match_found(cells: Array, match_type: String)
- match_cleared(cells: Array, score_gained: int)
- chain_started(chain_number: int)
- chain_ended(total_chain: int, total_score: int)
- board_settled()
- game_over_triggered(reason: String)
- level_up(new_level: int)
- score_changed(new_score: int, delta: int)
```

---

## Core Systems

### Grid System

**Recommended approach**: Custom grid logic over TileMap for puzzle games (more control over animations and special behaviors).

```text
Grid responsibilities:
- Store cell states (empty, occupied, block type)
- Provide neighbor queries (up, down, left, right, diagonal)
- Handle gravity (cells falling to fill gaps)
- Detect out-of-bounds
- Convert between grid coords and pixel coords

Grid data structure:
- 2D array (Array of Arrays) for simple games
- Dictionary with Vector2i keys for irregular boards
- Size: typically 6-8 columns × 12-14 rows for falling block games
```

### Match Detection

**BFS/Flood Fill** (for connected-color matching like Puyo):

```text
Algorithm:
1. For each cell, BFS to find all connected same-color cells
2. If group size >= threshold, mark for clearing
3. Process all groups simultaneously
4. After clearing, apply gravity
5. Repeat until no new matches (chain detection)

Performance note: Only check cells affected by the last placement/gravity, not entire board
```

**Line Detection** (for Tetris-style):

```text
Algorithm:
1. After block locks, check each row
2. If row is completely filled, mark for clearing
3. Clear marked rows simultaneously
4. Drop rows above cleared lines
5. No chain detection needed (or optional)
```

**Pattern Matching** (for swap/match-3):

```text
Algorithm:
1. After swap, scan rows and columns for 3+ consecutive same-color
2. Mark all matches
3. Clear simultaneously
4. Apply gravity to fill gaps
5. Check for new matches (cascade)
6. Repeat until stable
```

### Gravity System

```text
Implementation:
1. Process columns from bottom to top
2. For each empty cell, find nearest occupied cell above
3. Move block down (animate with Tween)
4. After all gravity resolves, check for new matches

Animation:
- Use Tween for smooth falling (0.1-0.2s per cell)
- Optional: accelerate falling speed for longer drops
- Optional: bounce/squish on landing for juice
```

### Input System

```text
For falling block games:
- Left/Right: move block laterally (with DAS — Delayed Auto Shift)
- Down: soft drop (accelerate fall)
- Up or Space: hard drop (instant placement)
- Z/X or A/D: rotate clockwise/counterclockwise
- DAS delay: 150-200ms initial, 50ms repeat

For swap/match games:
- Click/tap first block, click/tap second block to swap
- Or: drag block to adjacent cell
- Highlight valid moves optionally

For roguelike/card games:
- Click to select card/skill
- Click target cell/area to apply
- Confirm button for turn execution
```

### Spawn System

```text
For falling block games:
- Next piece queue: show at least 1-3 upcoming pieces
- Piece generation: bag system (all types equally distributed per bag) to reduce RNG frustration
- Spawn position: top-center of board
- Spawn timing: immediate after previous piece locks + match resolution

Color/type distribution:
- Start with 3-4 colors, add more as difficulty increases
- Never spawn more than N colors where N makes the board unsolvable
- Consider "mercy" spawns if board is dangerously full
```

---

## Visual Polish Patterns

### Screen Shake

```text
When to use: block landing (hard drop), large chain clears, game over
Implementation: offset Camera2D position with decaying amplitude
Parameters: intensity 2-8px, duration 0.1-0.3s, decay exponential
```

### Particle Effects

```text
Match clear: burst particles at each cleared cell (color-matched)
Chain multiplier: increasingly dramatic particles per chain level
Hard drop: dust/impact particles at landing
Level up: screen-wide celebration particles
```

### Tween-Based Animations

```text
Block movement: 0.05-0.1s ease_out
Block rotation: 0.08s ease_in_out
Match clear: 0.2s scale to 0, then free
Score popup: float up + fade out over 0.8s
Combo text: scale bounce 1.0 → 1.3 → 1.0 over 0.3s
```

### Juice Checklist (Apply to Every Feature)

```text
□ Does it animate? (nothing should "pop" into existence)
□ Does it have sound? (every action should have audio feedback)
□ Does it feel impactful? (screen shake, particles, flash)
□ Does it have anticipation? (preview, ghost, highlight)
□ Does it have follow-through? (settle, bounce, fade)
```

---

## Difficulty and Pacing

### Speed Curve (Falling Block Games)

```text
Level 1: 1.0s per cell (comfortable)
Level 2-5: decrease by 0.1s per level
Level 6-10: decrease by 0.05s per level
Level 11+: decrease by 0.02s per level
Max speed: never faster than 0.1s per cell (must remain playable)

Alternative: gravity measured in cells/frame (like NES Tetris)
- Level 1: 1/48 (one cell every 48 frames)
- Level 9: 1/6
- Level 13+: 1/4 to 1/2
- Level 29+: 1/1 (instant gravity, "kill screen")
```

### Difficulty Scaling Options

```text
- Increase fall speed
- Add more block colors/types
- Introduce garbage/obstacle blocks
- Reduce time for decisions (turn-based games)
- Spawn more complex block shapes
- Increase opponent AI aggression
- Add board modifiers (smaller board, blocked cells)
```

### Versus Mode — Garbage System

```text
Standard approach:
- When player clears blocks, send "garbage" to opponent
- Garbage appears as grey/obstacle blocks from bottom
- Amount sent = cells cleared × multiplier (chains multiply)
- Garbage has delay (1-2 turns) before appearing (gives counter opportunity)
- Player can reduce incoming garbage by clearing their own blocks

Balance parameters:
- Garbage per clear: 1 row per [threshold] cells cleared
- Chain multiplier: 2x, 3x, 4x... per chain level
- Counter window: 1-3 actions before garbage resolves
- Max pending garbage: cap to prevent instant death
```

---

## Common Gotchas (AI Must Handle)

### Infinite Loop Prevention

```text
Problem: match → clear → gravity → new match → clear → ... (infinite chain)
Solution: cap maximum chain length (e.g., 20) or timeout after N seconds
```

### Race Condition: Input During Animation

```text
Problem: player inputs during clear animation cause invalid state
Solution: disable input during animation phases, re-enable after board settles
Use state machine: PLAYING → ANIMATING → CHECKING → PLAYING
```

### Board Deadlock

```text
Problem: no valid moves exist (swap games) or board full with no matches
Solution: 
- Detect deadlock condition
- Shuffle board preserving block counts (swap games)
- Game over with grace period (falling games)
- Offer "bomb" power-up as last resort
```

### Off-by-One Grid Errors

```text
Problem: blocks appear at wrong position or overlap
Solution: always validate grid bounds before placement
- Check: 0 <= x < width AND 0 <= y < height
- Separate "visual position" from "grid position"
- Use Vector2i for grid, Vector2 for display
```

---

## Performance Guidelines

### For Puzzle Games (Generally Low Concern)

```text
- Grid sizes are small (< 200 cells) — brute force is fine
- Match detection runs infrequently — no need to optimize
- Focus performance budget on visual effects and animations
- Use object pooling for particles and score popups
- Preload all audio and textures at game start
```

### Mobile Considerations (If Targeting Mobile)

```text
- Touch input: larger tap targets (minimum 44px)
- Swipe detection: distinguish swipe from tap
- Screen orientation: portrait for puzzle games (one-hand play)
- Battery: limit particle count, reduce shader complexity
- Screen size: scale UI elements, use anchors
```

---

## Save/Load Pattern

```text
What to save:
- High scores (per mode)
- Unlocked content (characters, themes, modes)
- Settings (volume, controls, accessibility)
- Current run state (roguelike: mid-run progress)

Where to save:
- user://save_data.json (Godot user directory)
- Encrypt if contains progression that shouldn't be easily editable

When to save:
- After each completed game
- After settings change
- After unlock
- On app minimize/close (mobile)
- Auto-save mid-run every N turns (roguelike)
```

---

## Accessibility Baseline

### Always Implement

```text
- Color-blind mode: use shapes/symbols in addition to color
- Adjustable speed: let players slow down (never lock difficulty)
- Clear visual feedback: animations for every state change
- Audio cues: different sounds for different block types/actions
- Pause anytime: never trap the player
- Remappable controls (or at minimum, alternative key bindings)
```

### Consider

```text
- High contrast mode
- Screen reader support for menus
- One-handed play option
- Reduced motion mode (minimize flashing/shaking)
- Font size options for UI text
```
