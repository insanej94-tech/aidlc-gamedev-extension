# AI Opponent Design — Versus Puzzle Game Intelligence

## Overview

This document defines how the AI coding assistant designs and implements computer-controlled opponents for versus puzzle games. The opponent AI must feel fair, challenging, and personality-driven — giving the user a satisfying adversary to play against.

**Core Principle**: The AI opponent should feel like playing against a person, not a machine. It should have visible "personality," make occasional mistakes, and provide appropriate challenge at every skill level.

---

## Opponent Architecture

### Decision Engine Structure

```text
AI Opponent System:
├── Perception Layer      # What the AI "sees"
│   ├── Own board state
│   ├── Opponent board state (if visible)
│   ├── Available pieces/moves
│   └── Incoming threats (garbage, timer)
│
├── Strategy Layer        # What the AI "thinks"
│   ├── Current strategy selection
│   ├── Risk assessment
│   ├── Opportunity detection
│   └── Personality modifiers
│
├── Action Layer          # What the AI "does"
│   ├── Move selection
│   ├── Timing simulation (human-like delays)
│   └── Execution with imperfection
│
└── Personality Layer     # How the AI "feels"
    ├── Aggression level
    ├── Defensive tendency
    ├── Risk tolerance
    └── Emotional reactions
```

### Timing Simulation (Critical for Feel)

The AI must NOT play instantly — that feels unfair and robotic.

```text
Difficulty-based timing:
- Easy:    800-1500ms per decision (slow, deliberate)
- Medium:  400-800ms per decision (natural pace)
- Hard:    200-400ms per decision (fast but human-possible)
- Expert:  100-250ms per decision (pro player speed)

Additional timing rules:
- Add random variance (±20%) to avoid mechanical rhythm
- Longer pauses when "thinking hard" (complex board states)
- Faster responses for obvious moves
- Occasional hesitation before committing to risky plays
```

---

## Difficulty Levels

### Level 1: Beginner (초보) — "Learning Together"

**Purpose**: Let new players win most of the time while still providing interaction.

**Behavior**:
- Only sees 1-2 moves ahead (no chain planning)
- Misses obvious matches 30% of the time
- Never builds combos intentionally
- Random piece placement with basic "don't stack too high" logic
- Never sends garbage strategically
- Reaction time: 1000-1500ms

**Personality**: Friendly, makes mistakes, sometimes pauses as if confused

**Win rate target**: Player wins 70-80% of games

### Level 2: Casual (일반) — "Friendly Rival"

**Purpose**: Provide moderate challenge, occasional wins for AI.

**Behavior**:
- Sees 2-3 moves ahead
- Detects obvious chains and sometimes builds them
- Basic defensive play (clears when board gets high)
- Sends garbage when convenient (not strategically timed)
- Occasionally misses opportunities
- Reaction time: 500-900ms

**Personality**: Competent but not aggressive, plays steadily

**Win rate target**: Player wins 50-60% of games

### Level 3: Skilled (고수) — "Worthy Opponent"

**Purpose**: Challenge experienced players, require strategy to beat.

**Behavior**:
- Sees 3-5 moves ahead
- Actively builds chains and combos
- Strategic garbage timing (sends when opponent is vulnerable)
- Balances offense and defense
- Rarely misses good opportunities
- Adapts to player patterns slightly
- Reaction time: 300-600ms

**Personality**: Focused, strategic, occasionally aggressive

**Win rate target**: Player wins 40-50% of games

### Level 4: Expert (달인) — "The Boss"

**Purpose**: Ultimate challenge, only beatable with mastery.

**Behavior**:
- Deep look-ahead (5+ moves)
- Optimal chain building
- Perfect garbage timing (attack when opponent just placed a piece)
- Reads player patterns and counters them
- Almost never misses opportunities
- Makes deliberate sacrifices for bigger payoffs
- Reaction time: 150-350ms

**Personality**: Intimidating, relentless, occasionally shows "respect" for good plays

**Win rate target**: Player wins 20-30% of games

---

## AI Personality System

### Personality Archetypes

Each AI opponent can have a distinct personality that affects their play style:

#### Aggressive (공격형) — "Storm"

```text
Traits:
- Prioritizes sending garbage over building safely
- Takes risks for bigger combos
- Attacks immediately when possible
- Weak point: own board often gets messy

Player strategy to beat: survive the assault, counter-attack when they over-extend
```

#### Defensive (방어형) — "Wall"

```text
Traits:
- Keeps board very clean and organized
- Rarely takes risks
- Accumulates resources before striking
- Rarely sends garbage but when they do, it's devastating

Player strategy to beat: pressure them before they build up
```

#### Balanced (균형형) — "Mirror"

```text
Traits:
- Adapts to player's style
- If player is aggressive, plays defensively
- If player is passive, starts attacking
- Well-rounded, no obvious weakness

Player strategy to beat: vary your strategy, don't be predictable
```

#### Chaotic (변칙형) — "Wildcard"

```text
Traits:
- Unpredictable behavior
- Sometimes makes brilliant plays, sometimes makes mistakes
- Alternates between aggressive and passive
- Uses unusual strategies

Player strategy to beat: stay consistent, let them self-destruct
```

#### Speedster (속공형) — "Flash"

```text
Traits:
- Very fast piece placement
- Small, frequent clears (never builds big combos)
- Constant stream of small garbage
- Keeps pressure on relentlessly

Player strategy to beat: build big combos to overwhelm the small clears
```

---

## User-Facing Communication

### How to Present AI Difficulty

```text
AI says: "AI 상대 난이도를 정할까요?

🌱 쉬움 — 처음 배울 때, 편하게 이기면서 연습
⚔️ 보통 — 적당한 도전, 대부분 이길 수 있음
🔥 어려움 — 진짜 승부! 집중해야 이길 수 있음
💀 달인 — 최고 수준, 이기면 진짜 고수

어떤 걸로 할까요? (나중에 바꿀 수 있어요)"
```

### How to Present AI Personality (Optional, For Games With Multiple Opponents)

```text
"대전 상대를 골라보세요:

⚡ 폭풍이 — 미친 듯이 공격해옴, 방어가 약함
🛡️ 철벽이 — 꼼꼼하게 쌓다가 한 방에 터뜨림
🪞 거울이 — 내 스타일에 맞춰서 대응함
🎲 변칙이 — 예측 불가, 가끔 미친 플레이

누구랑 붙어볼래요?"
```

### Adaptive Difficulty (Silent)

If the user keeps winning or losing too much, adjust silently:

```text
If player wins 5+ games in a row:
  → Slightly increase AI difficulty
  → "오 계속 이기네요! [상대 이름]이 좀 더 열심히 하는 것 같아요 😤"

If player loses 3+ games in a row:
  → Slightly decrease AI difficulty
  → "이번 판은 [상대 이름]이 좀 느슨하네요, 찬스예요!"

Never tell the user you adjusted difficulty explicitly.
```

---

## Implementation Patterns (AI Internal)

### Basic Decision Loop

```text
Every AI "tick" (time step):
1. Evaluate current board state
2. Generate possible moves (top N candidates)
3. Score each move based on:
   - Chain potential (weighted by personality)
   - Board cleanliness (how organized the result is)
   - Garbage send potential
   - Risk level (how dangerous the resulting board is)
4. Apply personality modifier to scores
5. Apply imperfection (skill-based random degradation)
6. Select highest-scoring move
7. Wait appropriate time delay
8. Execute move
```

### Imperfection Injection

Making the AI feel human by introducing controlled mistakes:

```text
Imperfection types:
- "Blind spot": fail to see a match that exists (frequency based on difficulty)
- "Hesitation": take longer on easy decisions occasionally
- "Greed": go for a bigger combo when the safe play is better
- "Panic": play faster and worse when board is dangerously full
- "Fatigue": slight performance degradation in long games

Easy AI:   40% of decisions have imperfection applied
Medium AI: 20% of decisions have imperfection applied
Hard AI:   8% of decisions have imperfection applied
Expert AI: 3% of decisions have imperfection applied
```

### Board Evaluation Heuristics

```text
Score factors for move evaluation:
+100: Clears blocks (immediate reward)
+50 per chain level: Chain potential detected
+30: Keeps board flat (organized)
+20: Groups same colors together (future potential)
-50: Creates isolated blocks (hard to match later)
-100: Stacks past danger line
-200: Creates dead zones (unreachable blocks)

Personality modifiers:
Aggressive: +50 bonus to garbage-sending moves
Defensive:  +50 bonus to board-cleaning moves
Balanced:   No modifier (pure score)
Chaotic:    Random ±30 to all scores
Speedster:  +30 bonus to immediate clears (even small ones)
```

---

## Versus Mode Game Loop

### Standard Versus Flow

```text
1. Both players get same piece sequence (fairness)
   OR both get random sequences (variance)
2. Player and AI play simultaneously
3. Clears generate "garbage" sent to opponent
4. Garbage appears from bottom after delay
5. First to top-out loses
6. Best of 3/5/7 rounds (user choice)
```

### Garbage System Parameters

```text
Garbage generation:
- Single clear (3-4 blocks): 0-1 garbage rows
- Double clear: 1-2 garbage rows
- Chain x2: 2-3 garbage rows
- Chain x3: 3-4 garbage rows
- Chain x4+: 4-6 garbage rows
- All-clear (empty board): 6+ garbage rows

Garbage timing:
- Delay before landing: 1-3 seconds (gives counter opportunity)
- Counter mechanic: clearing blocks reduces incoming garbage
- Visual warning: show pending garbage on side of board
```

### Round Structure Communication

```text
AI says (presenting versus mode):
"대전 모드 설정:

🎮 몇 판 중 이기면 승리?
A) 단판 승부 (빠르게!)
B) 3전 2선승
C) 5전 3선승

⚡ 속도:
A) 느긋하게 시작 (연습용)
B) 보통 속도 (기본)
C) 빠르게 시작 (긴장감!)

어떻게 할까요?"
```

---

## AI Opponent for Roguelike Mode

### Boss Fight Design

In roguelike puzzle games, AI opponents can serve as bosses:

```text
Boss characteristics:
- Unique personality/mechanic per boss
- Telegraphed attacks (player can see what's coming)
- Escalating difficulty within the fight
- Satisfying defeat animation/reward

Example bosses:
Boss 1 "슬라임": Sends garbage slowly, easy to counter. Teaching fight.
Boss 2 "고블린": Fast attacker, small combos. Tests speed.
Boss 3 "마법사": Manipulates board (removes your pieces, changes colors).
Boss 4 "드래곤": Massive delayed attacks. Tests combo building.
Boss 5 "마왕": All previous mechanics combined. Final test.
```

### Enemy Variety Between Bosses

```text
Regular enemies (between bosses):
- Low difficulty, quick fights (30-60 seconds)
- Each has one quirk (only sends blue garbage, attacks every 5 seconds, etc.)
- Player gets to feel powerful against them
- Occasional elite enemy (harder, better reward)
```

---

## Testing and Balancing

### AI Should Self-Balance

When implementing AI opponents, the AI coding assistant should:

1. Create the opponent AI
2. Simulate matches internally (if possible)
3. Adjust parameters until win rates match targets
4. Present to user for playtesting
5. Refine based on user feedback

### User Feedback Interpretation

| User Says | Adjustment |
|---|---|
| "너무 쉬워" | Increase difficulty one step |
| "너무 어려워" | Decrease difficulty one step |
| "이길 수가 없어" | Reduce to easiest, add tutorial tips |
| "재미없어" (vs AI) | Change personality type, add more variety |
| "불공평해" | Check if AI has unfair advantages (speed, perfect info) |
| "로봇 같아" | Increase imperfection, add timing variance |
| "예측 가능해" | Add more personality variety, mix strategies |
