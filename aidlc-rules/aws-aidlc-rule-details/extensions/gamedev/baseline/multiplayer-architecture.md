# Multiplayer Architecture Guide — Playing Together

## Overview

This document defines how the AI implements multiplayer features in a puzzle game, from the simplest local 2-player to online play. The approach is progressive — start with the easiest option and upgrade when needed.

**Core Principle**: Start local, go online later. Local multiplayer is 10x simpler and provides immediate fun. Online multiplayer adds significant complexity and should only be attempted when the local version is solid.

---

## Multiplayer Progression Path

```text
Level 1: Solo + AI Opponent (already covered in ai-opponent-design.md)
    ↓ (user says "친구랑 같이 하고 싶어")
Level 2: Local Multiplayer (same device, split screen or shared board)
    ↓ (user says "멀리 있는 친구랑 하고 싶어")
Level 3: Online Multiplayer (separate devices, network play)
    ↓ (user says "모르는 사람들과도 하고 싶어")
Level 4: Matchmaking (public lobbies, ranking)
```

The AI should suggest the appropriate level based on user needs and never over-engineer.

---

## Level 2: Local Multiplayer

### Design Patterns

**Pattern A: Split Screen (Versus)**
```text
┌─────────────────────────────────┐
│  Player 1 Board │ Player 2 Board │
│                 │                │
│   [WASD]        │   [Arrow Keys] │
│                 │                │
│  Score: 1200    │  Score: 800    │
└─────────────────────────────────┘
```

**Pattern B: Shared Board (Co-op)**
```text
┌─────────────────────────────────┐
│         Shared Board            │
│                                 │
│  P1 cursor (blue) ←→ P2 cursor │
│            (red)                │
│                                 │
│  Combined Score: 2000           │
└─────────────────────────────────┘
```

**Pattern C: Hot Seat (Turn-Based)**
```text
Turn-based: players alternate on same board/device.
Best for: roguelike puzzles, strategy puzzles
"플레이어 1 차례!" → plays → "플레이어 2 차례!"
```

### Implementation (AI Internal)

```text
Local multiplayer requirements:
- Separate input handlers (P1: WASD/QE, P2: Arrows/Numpad)
- Separate game states per player (for versus)
- Shared viewport or split viewport
- Local garbage/attack system between players
- Shared screen real estate management

Godot implementation approach:
- Two instances of the game board scene
- InputEvent routing based on key mapping
- Signals between boards for garbage/effects
- Single game loop managing both boards
```

### User Communication

```text
AI says: "같이 할 수 있는 모드를 만들어볼까요?

A) 대전 모드 — 같은 컴퓨터에서 둘이 대결!
   (한 명은 WASD, 한 명은 방향키)

B) 협동 모드 — 같이 힘 합쳐서 클리어!

C) 번갈아 하기 — 한 명씩 번갈아가며 플레이

어떤 게 재밌을까요?"
```

### Input Configuration

```text
Default key mapping (versus):

Player 1:          Player 2:
W - Hard drop      ↑ - Hard drop
A - Move left      ← - Move left
S - Soft drop      ↓ - Soft drop
D - Move right     → - Move right
Q - Rotate left    Numpad 0 - Rotate left
E - Rotate right   Numpad . - Rotate right

AI creates this configuration silently.
If conflict or inconvenience reported, offers remapping.
```

---

## Level 3: Online Multiplayer

### When to Suggest Online

Only when:
- Local multiplayer is working well
- User explicitly asks for remote play
- The game concept requires it

```text
AI says: "온라인 대전은 좀 더 복잡한 작업이에요.
만들 수는 있는데, 먼저 확인할 게 있어요:

- 인터넷 연결이 필요해요 (당연히)
- 상대방도 같은 게임을 설치해야 해요
- 처음 설정이 좀 걸려요 (1-2시간)

지금 시작할까요, 아니면 나중에?"
```

### Architecture Options

#### Option A: Peer-to-Peer (P2P) — Simplest Online

```text
Pros:
- No server needed
- Free (no hosting costs)
- Lower latency between players

Cons:
- Both players must be online simultaneously
- One player hosts (asymmetric connection)
- NAT traversal can be tricky
- No matchmaking (need to share room code)

Best for: friends playing together
```

#### Option B: Dedicated Server — More Reliable

```text
Pros:
- Consistent experience for all players
- Can handle disconnections gracefully
- Enables matchmaking and ranking
- Authoritative (prevents cheating)

Cons:
- Requires server hosting (costs money)
- More complex to implement
- Latency depends on server location

Best for: public games, competitive play
```

#### Option C: Relay Service — Middle Ground

```text
Using services like:
- Steam Networking (if on Steam)
- Epic Online Services (free)
- Godot's built-in ENet with relay

Pros:
- Handles NAT traversal
- Often free tier available
- Simpler than raw networking

Best for: indie games with small player base
```

### Recommended Approach for Non-Developer

```text
AI recommends: Option A (P2P) with room codes

Why:
- Free (no server costs)
- Simple concept (share code with friend)
- Works well for 2-player puzzle games
- Can upgrade to server-based later if needed

User experience:
Player 1: "방 만들기" → gets code "ABC123"
Player 2: "방 참가" → enters "ABC123"
→ Connected! Game starts.
```

### Godot Networking Implementation (AI Internal)

```text
Architecture:
- Use Godot's high-level multiplayer API (MultiplayerAPI)
- ENet as transport layer
- One player is host (server), other is client
- Synchronize: piece spawns, board state, garbage events

Sync strategy for puzzle games:
- Deterministic simulation (both run same logic)
- Sync only: inputs + random seeds
- Verify: periodic board state checksum
- On desync: host state wins (authoritative)

Key signals:
- player_connected(id)
- player_disconnected(id)
- game_state_sync(state_data)
- garbage_sent(from_id, amount)
- game_over(loser_id)
```

### Handling Network Issues

```text
Lag/latency:
- Input buffer (2-3 frames)
- Smooth interpolation for opponent's board
- Never freeze local game for sync

Disconnection:
- Show "연결이 끊어졌어요..." message
- Auto-reconnect attempt (3 tries)
- If failed: "상대방이 나갔어요. AI로 대체할까요?"

Desync:
- Detect via periodic checksum
- Silently correct by accepting host state
- If persistent: report as bug (log for AI to fix)
```

---

## User Communication for Online Play

### Setting Up a Match

```text
AI says: "온라인 대전 방법:

🎮 방 만들기:
1. '방 만들기' 선택
2. 코드가 나와요 (예: ABC123)
3. 이 코드를 친구한테 보내세요!

🎮 방 참가하기:
1. '방 참가' 선택
2. 친구가 보내준 코드 입력
3. 연결!

정말 간단해요!"
```

### When Connection Fails

```text
AI says: "연결이 안 되네요 😅

확인해볼 것:
- 둘 다 인터넷 연결돼 있나요?
- 코드를 정확히 입력했나요?
- 방화벽이 차단하고 있을 수 있어요

다시 시도하기: '방 만들기'를 다시 눌러보세요.
계속 안 되면 말해주세요, 다른 방법 찾아볼게요!"
```

---

## Level 4: Matchmaking (Advanced)

### Only If Explicitly Requested

```text
AI says: "매치메이킹 (자동으로 상대 찾기) 은 서버가 필요해요.
무료 옵션도 있긴 한데, 설정이 좀 복잡하고
어느 정도 플레이어 수가 있어야 의미가 있어요.

지금은:
- 친구끼리 코드로 대전 ← 이게 현실적
- 나중에 유저가 늘면 매치메이킹 추가 가능

일단 친구 대전으로 시작하고, 나중에 필요하면 확장할까요?"
```

### If User Insists

```text
Matchmaking implementation path:
1. Simple lobby system (list of open rooms)
2. Random match (connect to first available room)
3. Skill-based matching (ELO or similar rating)
4. Ranked seasons (competitive ladder)

Each level requires more infrastructure.
AI recommends stopping at level 1-2 for indie games.
```

---

## Cross-Platform Considerations

### Web + Desktop Cross-Play

```text
Possible with WebSocket transport:
- Web player (HTML5 export) connects via WebSocket
- Desktop player connects via ENet or WebSocket
- Both implementations sync the same game state

AI handles protocol bridging silently.
```

### Mobile + Desktop Cross-Play

```text
Same approach as web:
- Touch controls for mobile
- Keyboard/controller for desktop
- Same network protocol underneath
- AI implements input abstraction layer
```

---

## Security Basics (AI Handles Silently)

```text
For casual friend-to-friend play:
- Room codes expire after 5 minutes if not joined
- Simple rate limiting on connection attempts
- No personal data transmitted (just game state)
- Room codes are random, not guessable

For public play (if later):
- Server validates all game actions
- Anti-cheat: verify board states match inputs
- Report system for toxic behavior
- IP-based rate limiting
```

---

## Testing Multiplayer

### AI's Approach

```text
1. Implement networking code
2. Test locally (two instances on same machine)
3. Present to user for real-world test with friend
4. Fix issues based on feedback

AI says: "온라인 기능을 만들었어요!
친구한테 연락해서 같이 테스트해볼 수 있어요?

테스트할 때 확인해볼 것:
- 연결이 잘 되는지
- 게임이 동시에 잘 돌아가는지
- 상대 보드가 실시간으로 보이는지
- 공격(가비지)이 제대로 전달되는지

문제 있으면 바로 말해주세요!"
```

### Common Issues and Fixes

| Issue | Likely Cause | AI Fix |
|---|---|---|
| 연결 안 됨 | NAT/방화벽 | UPnP 활성화 또는 relay 서버 사용 |
| 랙 심함 | 동기화 과다 | 입력만 전송, 로컬 시뮬레이션 |
| 상대 보드 끊김 | 패킷 손실 | 보간(interpolation) 추가 |
| 결과 다름 | 비결정적 로직 | 시드 동기화, 체크섬 검증 |
| 한쪽만 게임오버 | 타이밍 불일치 | 서버 시간 기준 통일 |
