# Game Design Interview — Structured Ideation Process

## Overview

This document defines the structured interview process that guides a non-developer user from "I want to make a puzzle game" to a concrete, buildable game concept. The AI conducts this interview conversationally, not as a rigid form.

**Trigger**: After onboarding is complete, or when a user starts a new game concept.

**Goal**: Extract enough design decisions to begin the first playable milestone, while leaving room for the game to evolve through playtesting.

**Key Principle**: Ask the minimum needed to start building. Don't over-plan — let the game reveal itself through play.

---

## Interview Flow

The interview has 5 stages. Each stage produces enough information to proceed, and the AI fills in gaps with smart defaults.

---

## Stage 1: Reference and Inspiration (레퍼런스 수집)

### Purpose

Understand the user's taste and mental model by connecting to games they already know.

### Questions

```text
🎮 어떤 퍼즐 게임을 좋아하세요? (여러 개 말해도 돼요!)

예를 들면:
- 뿌요뿌요, 테트리스, 컬럼스 같은 낙하형
- 캔디크러시, 퍼즐앤드래곤 같은 매칭형
- Slay the Spire, 밤피서바이버 같은 로그라이크
- 기타 좋아하는 퍼즐/전략 게임 뭐든!
```

### Follow-up Questions (pick 1-2 based on response)

```text
그 게임에서 제일 재밌는 순간이 뭐예요?
(예: 연쇄가 터질 때, 위기에서 탈출할 때, 새 카드 얻을 때...)
```

```text
반대로, 짜증나거나 지루한 순간은요?
(예: 운빨이 너무 심할 때, 같은 패턴 반복할 때...)
```

### AI Internal Processing

From the user's answers, classify:

- **Primary archetype**: 낙하형 / 매칭형 / 턴제 전략형 / 로그라이크
- **Joy source**: 연쇄(chain) / 위기탈출(clutch) / 성장(progression) / 전략(strategy) / 파괴(destruction)
- **Frustration source**: RNG / 반복 / 속도압박 / 복잡한 규칙
- **Implied preferences**: 속도(fast/slow), 깊이(casual/deep), 감정(tense/relaxing)

---

## Stage 2: Core Mechanic Selection (핵심 메카닉 선택)

### Purpose

Narrow down the core "verb" of the game — what the player actually DOES every moment.

### Present Options (tailored to Stage 1 answers)

```text
게임의 기본 동작을 정해볼게요.
플레이어가 매 순간 하는 행동이에요:

A) 떨어지는 블록을 배치한다 (뿌요뿌요/테트리스 스타일)
   → 빠른 판단, 쌓이는 긴장감

B) 보드 위 블록을 직접 움직여 맞춘다 (캔디크러시 스타일)
   → 여유로운 탐색, 패턴 찾기

C) 카드/스킬을 선택해서 블록에 영향을 준다 (로그라이크 스타일)
   → 전략적 선택, 성장하는 재미

D) 위의 것들을 섞고 싶어요 (어떤 조합인지 말해주세요!)

E) 다른 아이디어가 있어요

뭐가 끌리세요?
```

### AI Internal Processing

Map selection to implementation approach:

- **A (낙하형)**: Grid + gravity + spawn system + rotation + time pressure
- **B (매칭형)**: Grid + swap/drag + match detection + cascade + no time pressure (or optional)
- **C (로그라이크)**: Grid + card/skill system + turn-based + meta progression + procedural generation
- **D (하이브리드)**: Identify which elements to combine, create phased implementation plan

---

## Stage 3: Game Mode and Structure (게임 모드와 구조)

### Purpose

Determine how a "play session" works — length, opponent, win condition.

### Questions (adapt based on Stage 2)

```text
게임 한 판이 어떤 느낌이면 좋겠어요?

⏱️ 한 판 길이:
A) 짧고 빠르게 (1-3분) — 지하철에서 한 판
B) 적당히 (5-10분) — 집중해서 한 판
C) 길게 (15분+) — 하나의 "런"을 깊게

🆚 상대:
A) 혼자서 높은 점수/생존 도전
B) AI 상대와 대전
C) 다른 사람과 대전 (로컬/온라인)
D) 혼자 + 대전 모드 둘 다

🏁 끝나는 조건:
A) 보드가 가득 차면 끝 (생존형)
B) 스테이지/레벨 클리어 (목표달성형)
C) 체력이 다하면 끝 (대전형)
D) 정해진 턴 안에 최고 점수 (제한형)
```

### AI Internal Processing

Combine answers into a session structure:

- Session length → determines pacing and complexity cap
- Opponent type → determines AI opponent needed, multiplayer architecture
- End condition → determines core tension loop and score system

---

## Stage 4: Visual Style and Theme (비주얼과 분위기)

### Purpose

Establish the look and feel without requiring art skills.

### Questions

```text
게임 분위기가 어땠으면 좋겠어요?

🎨 전체 느낌:
A) 귀엽고 밝은 (파스텔, 동글동글)
B) 깔끔하고 모던한 (미니멀, 세련된)
C) 레트로/픽셀 (옛날 게임 감성)
D) 몽환적/신비로운 (별, 우주, 마법)
E) 강렬하고 역동적 (네온, 빠른 이펙트)
F) 다른 느낌이 있어요 (설명해주세요!)

🧩 블록/퍼즐 조각 모양:
A) 동그란 캐릭터 (뿌요뿌요처럼 표정 있는)
B) 보석/크리스탈
C) 깔끔한 기하학 도형
D) 음식/과일
E) 원소/마법 기호
F) 다른 거! (뭐든 말해주세요)
```

### AI Internal Processing

Map to implementable art direction:

- Style → color palette, shader approach, animation style
- Block shape → sprite design direction, particle effects style
- Combined → overall UI theme, font choice, background style

**Note**: The AI will implement with placeholder graphics first, then refine. The user does not need to provide art assets.

---

## Stage 5: Unique Twist (차별화 요소)

### Purpose

Find what makes THIS game different from its references. This is where creativity happens. Apply principles from `fun-design-framework.md` (tension-release, agency, surprise) when evaluating twist ideas.

### Prompt

```text
좋아요! 기본 틀이 잡혔어요. 이제 이 게임만의 특별한 점을 하나 정해볼까요?

레퍼런스 게임들과 다른, 이 게임만의 재미 포인트요.
아이디어가 있으면 말해주세요! 없으면 제가 몇 가지 제안할게요.
```

### If User Has No Idea — Suggest Based on Previous Answers

The AI generates 3-4 unique twist suggestions tailored to the chosen archetype:

**For 낙하형 (falling block)**:

```text
이런 건 어때요?

A) 중력 방향이 바뀐다 — 가끔 블록이 위로, 옆으로 떨어짐
B) 블록이 진화한다 — 같은 색끼리 붙으면 합쳐져서 더 큰 블록이 됨
C) 날씨 시스템 — 비 오면 빨리 떨어지고, 바람 불면 옆으로 밀림
D) 블록에 특수 능력 — 폭탄, 얼음, 무지개 등 랜덤으로 등장
```

**For 매칭형 (match/swap)**:

```text
이런 건 어때요?

A) 매칭할 때마다 보드 지형이 변한다 — 구멍 생기고, 벽 올라오고
B) 적이 있다 — 매칭으로 공격하고, 적이 보드를 방해함
C) 시간 되감기 — 하루에 3번 "되돌리기" 가능
D) 연금술 — 특정 조합으로 매칭하면 새로운 색/타입 블록 생성
```

**For 로그라이크 (roguelike)**:

```text
이런 건 어때요?

A) 매 층마다 퍼즐 규칙이 바뀐다 — 이번 층은 매칭 4개, 다음 층은 3개
B) 획득한 아이템이 보드를 변형 — 보드 크기, 블록 종류, 중력 등
C) 적도 퍼즐을 푼다 — 누가 더 빨리/잘 푸느냐 경쟁
D) 희생 시스템 — 큰 효과를 쓰려면 보드 일부를 포기해야 함
```

---

## Interview Completion

### AI Behavior After Interview

1. Summarize the concept in 5-6 sentences (plain language)
2. Show a visual mockup of the game board (ASCII art)
3. Present the first 5 milestones
4. Ask for confirmation: "이 방향으로 시작할까요?"
5. On confirmation, immediately begin milestone 1

### Summary Template

```text
📋 우리 게임 컨셉 정리!

[게임 이름은 나중에 정해도 돼요]

🎮 핵심: [한 문장으로 핵심 메카닉]
🆚 모드: [어떻게 플레이하는지]
⏱️ 한 판: [예상 플레이 시간]
🎨 분위기: [비주얼 방향]
⭐ 차별점: [유니크한 요소]

이런 느낌이에요:
[ASCII art로 보드 시각화]

📍 처음 5단계:
1. [마일스톤 1 — 가장 기본]
2. [마일스톤 2]
3. [마일스톤 3]
4. [마일스톤 4]
5. [마일스톤 5 — 여기까지 하면 "게임"이 됨]

이 방향으로 시작할까요? 바꾸고 싶은 부분 있으면 편하게 말해주세요!
```

---

## Interview Rules

### DO

- Keep each stage to 1-2 questions max (don't overwhelm)
- Accept short/vague answers and fill in intelligently
- Offer "잘 모르겠어요, 알아서 해주세요" as a valid option at every stage
- Maintain enthusiasm and momentum
- Move quickly — the whole interview should take 5-10 minutes max
- Skip stages if the user already provided enough info organically

### DON'T

- Turn this into a 20-question survey
- Require definitive answers (everything can change later)
- Use game design jargon (ludonarrative, procedural generation, etc.)
- Make the user feel like wrong answers exist
- Delay building — if you have enough info after Stage 2, start building and ask remaining questions between milestones
- Present all stages at once — it should feel like a conversation, not a form

### SHORTCUT

If the user comes in with a clear vision ("뿌요뿌요 같은데 중력이 바뀌는 게임"):

- Skip to Stage 5 confirmation
- Fill in reasonable defaults for everything else
- Start building immediately
- Ask refinement questions between milestones
