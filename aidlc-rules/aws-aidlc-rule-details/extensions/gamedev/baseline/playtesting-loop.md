# Playtesting Loop — Feedback and Iteration

## Overview

This document defines the cyclical process of building → testing → getting feedback → improving. This is the heartbeat of game development with a non-developer user.

**Core Loop**:

```text
┌─────────────────────────────────────────┐
│                                         │
│   AI builds  →  User plays  →  User     │
│   milestone     and tests      gives    │
│                                feedback  │
│       ↑                          │      │
│       │                          ↓      │
│       └──── AI improves ←────────┘      │
│                                         │
└─────────────────────────────────────────┘
```

---

## Phase 1: Presenting the Build

### When a Milestone is Complete

The AI MUST:

1. Confirm completion
2. Give exact run instructions
3. Tell user WHAT to test (specific actions)
4. Tell user WHAT to notice (expected outcomes)
5. Invite open-ended feedback

### Template

```text
✅ [마일스톤 이름] 완성!

▶️ 실행 방법: Godot에서 F5

🎯 이것들을 해보세요:
 1. [구체적 동작 — 예: "방향키 좌우로 블록을 움직여보세요"]
 2. [구체적 동작 — 예: "블록이 바닥에 닿을 때까지 기다려보세요"]
 3. [구체적 동작 — 예: "같은 색 블록 옆에 놓아보세요"]

👀 이런 게 보여야 정상이에요:
 - [예상 결과 1]
 - [예상 결과 2]

느낌이 어때요? 뭐든 편하게 말해주세요! 🎮
```

---

## Phase 2: Receiving Feedback

**Note**: The authoritative feedback interpretation table is in `non-developer-mode.md`. This section provides additional context for the playtest-specific feedback loop.

### Feedback Types the AI Must Handle

| Feedback Style | Example | AI Interpretation |
|---|---|---|
| 감정적 반응 | "오 재밌다!" / "음..." | 긍정 → 방향 유지, 미온 → 개선점 탐색 |
| 비교 | "뿌요뿌요보다 느려" | 속도 파라미터 조정 대상 파악 |
| 요청 | "블록이 더 빨리 떨어졌으면" | 직접적 파라미터 변경 |
| 불만 | "이상해" / "별로야" | 구체화 질문 1회, 그 후 최선 추측으로 개선 |
| 아이디어 | "여기서 폭탄이 나오면?" | 백로그 추가 또는 즉시 구현 (크기에 따라) |
| 무반응 | (아무 말 없음) | 30초 후 "어떠세요?" 한번 물어보기 |

### Feedback Elicitation (If User Is Quiet)

```text
첫 번째 시도:
"어떠세요? 재밌어요? 뭔가 이상한 거 있어요?"

두 번째 시도 (구체적으로):
"블록 떨어지는 속도는 괜찮아요? 너무 빠르거나 느리진 않아요?"

세 번째 (선택지 제공):
"지금 느낌이 어떤 쪽이에요?
A) 괜찮은데 뭔가 부족해
B) 생각한 거랑 좀 달라
C) 좋아! 다음 단계 ㄱㄱ"
```

### Never Ask

- "코드를 확인해보셨나요?"
- "어떤 에러가 나오나요?" (초반에)
- "구체적으로 어떤 함수가..." 
- "기술적으로 설명해주실 수 있나요?"

---

## Phase 3: Interpreting and Acting on Feedback

### Quick Fixes (Do Immediately)

If the feedback maps to a simple parameter change:

- Speed adjustments (떨어지는 속도, 애니메이션 속도)
- Size changes (블록 크기, 보드 크기)
- Color/visual tweaks
- Sound volume
- Timing adjustments

```text
AI says: "속도 올렸어요! 다시 해보세요 ▶️"
```

No need for lengthy explanation — just fix and let them test again.

### Medium Changes (Implement in Next Micro-Milestone)

If feedback requires new logic but is small:

- Adding a visual indicator
- Changing a game rule slightly
- Adding feedback (sound, animation) to an existing action

```text
AI says: "좋은 지적이에요! 지금 바로 고칠게요... (1-2분)"
Then: "✅ 적용했어요! 확인해보세요."
```

### Large Changes (Becomes Next Milestone)

If feedback implies a new feature or significant rework:

```text
AI says: "오 좋은 아이디어! 그건 다음 단계에서 추가할게요.
지금 하고 있는 [현재 마일스톤] 마무리하고 바로 넘어갈게요!"
```

Add to milestone list and continue current work.

### Conflicting Feedback

If new feedback contradicts a previous decision:

```text
AI says: "전에 [이전 결정]으로 정했었는데, 생각이 바뀐 거죠?
바꿔도 전혀 문제 없어요! [바꿀 내용] 이렇게 하면 될까요?"
```

Update `GAME_STATUS.md` with the changed decision.

---

## Phase 4: Rapid Iteration

### The "Feel" Loop

For game feel adjustments (speed, timing, impact), use rapid iteration:

```text
Round 1: "속도를 30% 올렸어요. 해보세요!"
Round 2: "더 빠르게요? 또 올렸어요. 이번엔?"  
Round 3: "아 좀 많았어요? 중간으로 맞췄어요. 어때요?"
Round 4: "딱이에요? ✅ 좋아요, 이걸로 가겠습니다!"
```

This loop should be fast — no need for formal milestone structure. It's fine-tuning within a milestone.

### A/B Testing (When User Can't Decide)

```text
AI says: "두 가지 버전을 만들어볼게요!

A버전으로 먼저 실행해보세요... (F5)
느낌 봤으면, 제가 B버전으로 바꿀게요... 이제 다시 F5!

어떤 게 더 좋아요?"
```

---

## Feedback Documentation

### AI Maintains Internally

- All user feedback with timestamps
- Which changes were made in response
- User satisfaction progression (based on emotional cues)
- Recurring themes (if user keeps saying "느려" across milestones, there might be a systemic pacing issue)

### In GAME_STATUS.md

Record key design decisions that came from playtesting:

```markdown
## 플레이테스트에서 정해진 것들
- 블록 떨어지는 속도: 초당 2칸 (기본) → 사용자가 "딱 좋아"라고 한 수치
- 매칭 기준: 3개 연결 (4개는 "너무 어렵다"고 함)
- 화면 흔들림: 약하게 (강하면 "정신없다"고 함)
```

---

## Playtest Cadence

### Ideal Rhythm

```text
1. AI builds (10-20분)
2. User tests (2-5분)  
3. User gives feedback (1분)
4. AI adjusts (1-5분)
5. User confirms (1분)
→ Next milestone
```

### Signs the Loop Is Working

- User is engaged and giving specific feedback
- Changes are getting smaller (converging on "good feel")
- User starts making forward-looking requests ("다음엔 이거 해보자!")

### Signs the Loop Is Broken

- User stops responding or gives only "네", "좋아요"
  → Try prompting with specific questions or propose something exciting
- User seems frustrated or lost
  → Deliver a quick win (small, satisfying milestone)
- Long gaps between tests
  → Milestones might be too big — split them smaller
- User keeps changing their mind
  → Might need to revisit game-design-interview.md fundamentals

---

## Special Playtest Scenarios

### First Playtest Ever

Extra encouraging. Make the user feel like a game designer:

```text
"🎉 이게 당신의 첫 번째 게임이에요!
아직 초기 단계지만, 이미 '블록이 떨어져서 쌓이는' 게임이 돌아가고 있어요.
여기서부터 점점 재미있는 게 추가될 거예요. 어떠세요?"
```

### After Major Feature Addition

Guide attention to the new thing:

```text
"이번에 크게 바뀌었어요! 특히 [새 기능]을 집중적으로 테스트해주세요.
여러 번 해보면서 '이게 재미있나?' 느껴봐주세요."
```

### Before "Done" Declaration

When the game feels complete enough for a first version:

```text
"지금 상태가 꽤 완성된 것 같아요! 한번 처음부터 끝까지 쭉 플레이해보세요.
마치 처음 보는 게임처럼 해보는 거예요.

끝까지 하고 나서 전체적인 느낌 알려주세요:
- 처음 시작할 때 바로 이해됐는지
- 중간에 지루한 구간은 없었는지
- 또 하고 싶은 마음이 드는지"
```
