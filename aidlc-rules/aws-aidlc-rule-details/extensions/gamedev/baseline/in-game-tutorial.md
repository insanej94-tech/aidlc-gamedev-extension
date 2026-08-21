# In-Game Tutorial — Teaching Players Without Words

## Overview

This document defines how the AI builds in-game tutorials and first-play experiences. The goal is: any person who opens the game should understand how to play within 10 seconds, without reading a manual.

**Core Principle**: Show, don't tell. The best tutorial is invisible — the player learns by doing, not by reading.

---

## Tutorial Philosophy

### Rules of Good Game Tutorials

1. **Never stop the action** — avoid modal popups that block gameplay
2. **One concept at a time** — don't teach everything at once
3. **Let players discover** — guided discovery > instruction
4. **Forgive mistakes** — early game should be forgiving
5. **Respect intelligence** — don't over-explain obvious things
6. **Make it skippable** — returning players shouldn't suffer through it

### Anti-Patterns (Never Do These)

- Wall of text explaining controls before gameplay starts
- Forced tutorial that can't be skipped
- Pausing the game to explain every element
- Assuming the player read a manual
- Teaching advanced concepts before basics are mastered

---

## Tutorial Structure: Progressive Disclosure

### Layer 1: Instant Understanding (First 3 Seconds)

The player should understand the basic "verb" immediately:

```text
For falling block games:
- Show a block falling → player instinctively tries to move it
- Movement keys shown subtly on screen (fades after first use)

For match games:
- Show a highlighted pair → player taps/clicks to swap
- First swap always results in a match (guaranteed success)

For roguelike puzzles:
- Show enemy + board → player understands "solve puzzle to attack"
```

**Implementation**: The first 5 seconds of gameplay are carefully scripted to guarantee a successful first action.

### Layer 2: Core Mechanic (First 30 Seconds)

Teach the primary mechanic through constrained play:

```text
Technique: "Safe Space Learning"
- First few pieces/moves are easy (guaranteed matches available)
- Board starts mostly empty (low pressure)
- Speed is slow (plenty of time to think)
- Visual hints highlight valid moves (subtle glow or pulse)
```

### Layer 3: Consequences (First 2 Minutes)

Teach win/lose conditions naturally:

```text
- Board fills up → screen edges pulse red → player understands danger
- Match clears → satisfying effect → player understands the goal
- Score goes up → positive feedback → player understands progress
- Game over occurs → brief "try again?" → no punishment, fast restart
```

### Layer 4: Depth (First 5 Minutes)

Introduce advanced mechanics one at a time:

```text
- Combo/chain happens accidentally → big celebration → player wants to do it again
- Special block appears → behaves visibly differently → player experiments
- Difficulty ramps → player adapts or dies → learns pacing
```

### Layer 5: Mastery (Ongoing)

Advanced techniques revealed through play:

```text
- T-spin / advanced rotations (for falling blocks)
- Chain setup (for match games)
- Optimal card combinations (for roguelike)
- These are never explicitly taught — discovered through skill growth
```

---

## Visual Hint System

### Types of Visual Hints

| Hint Type | When to Use | How It Looks |
|---|---|---|
| Key prompt | First time a control is available | Small icon near relevant area, fades after use |
| Glow/pulse | Valid move available | Subtle outline pulse on interactable elements |
| Arrow/pointer | Critical first action | Animated arrow pointing to target |
| Ghost preview | Showing outcome of an action | Transparent preview of result |
| Highlight | Drawing attention to new element | Brief bright flash then normal |

### Hint Lifecycle

```text
1. Hint appears when context is appropriate
2. Stays visible until player performs the action
3. Fades out after successful execution
4. Never appears again for that action (flag in save data)
5. Exception: after long absence, hints can reappear once
```

### Implementation Pattern

```text
HintManager (Autoload) responsibilities:
- Track which hints have been shown/completed
- Show hints only when player seems stuck (5+ seconds without input)
- Never show more than one hint at a time
- Persist hint completion state in save file
- Reset hints only if player explicitly requests ("조작법 다시 보기")
```

---

## First-Time User Experience (FTUE) Script

### For Falling Block Puzzle

```text
[Game starts immediately — no title screen on first play]

Second 0-3: Block appears at top, starts falling slowly (2x slower than normal)
            Control hint appears: "← →" near the block (subtle, small)

Second 3-7: Player moves block (or it lands on its own)
            If player moved: hint disappears, brief "✓" flash
            If player didn't move: arrow pulses toward arrow keys

Second 7-15: Second block falls
             This time no hint — player is expected to move it
             Rotation hint appears only if block is in suboptimal orientation
             
Second 15-30: Blocks accumulate
              When 3+ same color are adjacent: brief glow on them
              If player doesn't notice: glow intensifies
              
Second 30-45: First match occurs (either by player action or scripted)
              BIG celebration — particles, sound, score popup
              This is the "aha!" moment — player understands the goal
              
Second 45+: Normal gameplay begins
            Speed gradually increases to normal
            No more hints unless player seems stuck
```

### For Match/Swap Puzzle

```text
[Game starts with board mostly filled]

Second 0-3: One pair of adjacent blocks glows (guaranteed match)
            Tap/click hint appears

Second 3-7: Player swaps the highlighted pair
            Match occurs → celebration
            If player doesn't act: hint arrow points to pair

Second 7-15: Another easy match is highlighted (less obviously)
             Player should find it with less help

Second 15-30: Hints stop
              Player plays freely
              Board is designed to have many easy matches available

Second 30+: Normal gameplay
            No guaranteed easy matches
            Real difficulty begins
```

### For Roguelike Puzzle

```text
[First room is a "tutorial room" with weakest enemy]

Turn 1: Show enemy with low HP
        Show board with obvious powerful move available
        Highlight the move

Turn 2-3: Let player experiment
          Enemy attacks very weakly (can't die in tutorial)
          
Turn 4-5: Player defeats enemy
          Celebration + first reward choice
          Reward choice teaches the meta-game loop

Room 2: Slightly harder enemy, no hints
        Player applies what they learned
        
Room 3+: Real game begins
```

---

## "Help" System (For Stuck Players)

### Passive Help (AI Detects Stuckness)

```text
Stuckness signals:
- No input for 10+ seconds (in action game)
- Same incorrect action repeated 3+ times
- Board hasn't changed in 30+ seconds
- Player restarted 5+ times without progress

Response (escalating):
Level 1: Subtle glow on a valid move (5 seconds)
Level 2: More obvious highlight with particle trail (10 seconds)
Level 3: Brief text appears: "이쪽을 보세요 →" (15 seconds)
Level 4: Auto-complete one move to demonstrate (20 seconds)
```

### Active Help (Player Requests)

```text
Pause menu includes:
- "조작법 보기" — shows control reference
- "힌트" — highlights one valid move
- "건너뛰기" — skips current challenge (roguelike)

These do NOT exist in versus mode (fairness).
```

---

## Tutorial for Different Audiences

### When Game Creator Shares with Friends

The tutorial must work for people who:
- Have never seen this specific game
- May not play puzzle games regularly
- Are on various skill levels
- Might be playing on different devices (web, desktop)

### Adaptive First Experience

```text
AI implements:
- Detect if player is experienced (fast actions, good decisions)
  → Skip remaining tutorial elements, go to full speed quickly
- Detect if player is struggling (slow, repeated failures)
  → Keep training wheels on longer, keep speed slow
  → Offer more hints, be more forgiving

This adaptation happens silently — no "select your skill level" popup.
```

---

## Tutorial Design by Genre

### Competitive Versus (뿌요뿌요 Style)

```text
Tutorial covers:
1. Move and place blocks (3 seconds)
2. Match same colors (10 seconds)
3. Chain combos (30 seconds — optional, can learn by accident)
4. Garbage concept (shown in first vs match — "that grey stuff comes from your opponent!")

Skip if: player immediately starts placing pieces with purpose
```

### Roguelike Puzzle

```text
Tutorial covers:
1. Board interaction (how to make moves)
2. Damage connection (matching = attacking enemy)
3. Taking damage (enemy turn)
4. Reward/upgrade selection (first choice after first win)
5. Death and retry (first death is scripted to teach the loop)

Do NOT teach: advanced combos, meta-strategy, optimal builds
(These are discovery moments — let players find them)
```

---

## AI Implementation Rules

### When Building Tutorial

1. Design the FTUE script before building the feature
2. First playable milestone should include tutorial scaffolding
3. Tutorial is tested by imagining a person who has never seen this game
4. Playtest specifically asks: "처음 시작할 때 바로 이해됐어요?"
5. Iterate on tutorial based on external feedback

### Communication with User (Game Creator)

```text
AI says: "게임에 '처음 하는 사람 안내'를 넣었어요.

처음 시작하면:
- 조작법이 자연스럽게 보여지고
- 첫 매칭이 쉽게 일어나게 해놨어요
- 30초 안에 게임 방법을 알 수 있을 거예요

친구한테 테스트 시킬 때, '아무 설명 없이 그냥 줘보세요' — 
설명 없이도 알아서 할 수 있으면 성공이에요!"
```

### Tutorial Completion Tracking

```text
Save data stores:
- tutorial_movement_done: bool
- tutorial_first_match_done: bool
- tutorial_combo_seen: bool
- tutorial_game_over_seen: bool
- hint_count_used: int

On subsequent plays: all tutorial elements skipped
On "조작법 다시 보기": temporarily re-enable hints
```
