# Milestone Progression Guide

## Overview

This document defines how game development progresses through milestones — small, playable increments that build toward a complete game. Each milestone MUST produce a runnable result the user can immediately test.

**Core Principle**: The user should be able to play something within 15-30 minutes of starting, and the game should grow richer with each milestone.

---

## Milestone Structure

### Every Milestone Must Have

1. **Clear goal** — one sentence describing what this milestone adds (in game terms)
2. **Playable result** — the game runs after this milestone and something new is visible/interactive
3. **Test prompt** — specific instruction for what the user should try
4. **Completion message** — tells user what's done, what to test, what's next

### Milestone Size Rules

- Maximum implementation time: 20-30 minutes of AI work
- If a feature takes longer, split it into sub-milestones
- Each milestone adds exactly ONE new visible/playable thing
- Never batch multiple features into one milestone

---

## Standard Milestone Tracks

The AI selects and adapts one of these tracks based on the game concept from the design interview.

### Track A: Falling Block Game (뿌요뿌요/테트리스 계열)

```text
Phase 1: 기본 동작 (첫 세션에서 완료 목표)
─────────────────────────────────
M1. 빈 게임 보드가 보인다
    → 격자가 그려진 보드 확인

M2. 블록 하나가 위에서 떨어진다
    → 블록이 천천히 내려오는 것 확인

M3. 블록을 좌우로 움직일 수 있다
    → 방향키로 블록 이동 확인

M4. 블록이 바닥에 닿으면 멈추고 새 블록이 나온다
    → 블록이 쌓이는 것 확인

M5. 같은 색 블록이 연결되면 터진다
    → 매칭 후 사라지는 것 확인

Phase 2: 게임성 추가
─────────────────────────────────
M6. 점수가 올라간다
    → 화면에 점수 표시, 블록 터질 때 올라감

M7. 연쇄 콤보가 작동한다
    → 연쇄 시 추가 점수 + 콤보 표시

M8. 게임 오버가 된다
    → 보드가 가득 차면 게임 끝 화면

M9. 다시하기 버튼이 생긴다
    → 게임 오버 후 재시작 가능

M10. 시간이 지나면 점점 빨라진다
     → 레벨업 느낌 확인

Phase 3: 완성도
─────────────────────────────────
M11. 다음 블록 미리보기
     → 다음에 올 블록 표시

M12. 하드 드롭 (즉시 떨어뜨리기)
     → 스페이스바로 바로 떨어뜨리기

M13. 블록 회전
     → 회전 키로 블록 돌리기

M14. 효과음과 배경음
     → 소리 확인

M15. 메인 메뉴 화면
     → 시작 화면에서 게임 시작

Phase 4: 확장 (선택)
─────────────────────────────────
M16+. 대전 모드 / 로그라이크 요소 / 특수 블록 / 스킨 등
      → 게임 디자인 인터뷰에서 정한 차별화 요소 구현
```

### Track B: Match/Swap Game (캔디크러시 계열)

```text
Phase 1: 기본 동작
─────────────────────────────────
M1. 블록이 채워진 보드가 보인다
    → 색깔 블록으로 가득 찬 격자 확인

M2. 블록을 클릭할 수 있다
    → 클릭하면 블록이 선택(하이라이트)됨

M3. 인접한 블록과 교환할 수 있다
    → 두 블록 위치 바뀜 확인

M4. 3개 이상 연결되면 터진다
    → 매칭 후 사라지는 것 확인

M5. 빈 자리에 블록이 떨어진다
    → 위에서 새 블록 채워짐

Phase 2: 게임성 추가
─────────────────────────────────
M6. 연쇄(캐스케이드)가 작동한다
    → 떨어진 후 새 매칭 자동 발생

M7. 점수 시스템
    → 매칭마다 점수, 연쇄 보너스

M8. 이동 횟수 제한 (또는 시간 제한)
    → 턴/시간 표시, 소진 시 게임 종료

M9. 목표 시스템
    → "빨간 블록 20개 터뜨리기" 같은 목표

M10. 스테이지 클리어와 다음 스테이지
     → 목표 달성 시 다음 판

Phase 3: 완성도
─────────────────────────────────
M11. 특수 블록 (4개 매칭 = 폭탄 등)
M12. 효과음과 이펙트
M13. 스테이지 선택 화면
M14. 별점 시스템 (1~3성)
M15. 메인 메뉴

Phase 4: 확장
─────────────────────────────────
M16+. 보스전 / 특수 스테이지 / 아이템 / 캐릭터 등
```

### Track C: Roguelike Puzzle Game

```text
Phase 1: 기본 동작
─────────────────────────────────
M1. 보드와 블록이 보인다
    → 기본 퍼즐 보드 확인

M2. 기본 매칭/제거가 작동한다
    → 기본 퍼즐 메카닉 동작

M3. 턴 기반으로 작동한다
    → 내 턴 → 결과 확인 → 다음 턴

M4. 적(또는 장애물)이 있다
    → 매칭하면 적에게 데미지

M5. 체력 시스템
    → 내 체력 / 적 체력 표시

Phase 2: 로그라이크 요소
─────────────────────────────────
M6. 적을 물리치면 다음 방으로
    → 스테이지 진행

M7. 스킬/아이템 선택지 등장
    → 전투 사이에 강화 선택

M8. 스킬이 퍼즐에 영향을 준다
    → 선택한 스킬로 보드 조작

M9. 죽으면 처음부터 (영구 사망)
    → 로그라이크 루프 완성

M10. 매 런이 다르다 (랜덤 요소)
     → 적 순서, 선택지 랜덤

Phase 3: 완성도
─────────────────────────────────
M11. 보스 적
M12. 다양한 일반 적
M13. 아이템/스킬 풀 확대
M14. 런 사이 영구 업그레이드
M15. 메인 메뉴 + 도감

Phase 4: 확장
─────────────────────────────────
M16+. 새 캐릭터 / 이벤트 / 스토리 / 일일 도전 등
```

---

## Milestone Execution Protocol

**Related files** (consult during milestone execution):
- `godot-puzzle-patterns.md` — implementation patterns for puzzle mechanics
- `playtesting-loop.md` — feedback collection after each milestone
- `version-control-guide.md` — auto-commit and save strategy
- `accessibility.md` — inclusive design applied to every feature
- `fun-design-framework.md` — juice and feel applied to every interaction

### Before Starting a Milestone

```text
AI says:
"🔨 [M번호]. [마일스톤 이름] 만들기 시작할게요!
완성되면 [눈에 보이는 결과]를 확인할 수 있어요."
```

### During Implementation

- Work silently — no progress updates for short milestones (< 10 min)
- For longer milestones, one mid-point update: "절반쯤 왔어요!"
- If the AI encounters an issue, resolve it silently (see error-handling-for-beginners.md)

### After Completing a Milestone

```text
AI says:
"✅ [마일스톤 이름] 완성!

🎮 실행해서 확인해보세요:
 - [구체적으로 뭘 해보라는 지시 1]
 - [구체적으로 뭘 해보라는 지시 2]

실행 방법: Godot에서 F5 (▶ 버튼)

어떠세요? 느낌 말해주세요!"
```

### Progress Display (Updated After Each Milestone)

```text
"📊 전체 진행:
 ✅ M1. 빈 보드 보이기
 ✅ M2. 블록 떨어지기
 ✅ M3. 블록 좌우 이동
 🔨 M4. 블록 쌓이기 ← 지금 여기
 ⬜ M5. 블록 매칭
 ⬜ M6. 점수
 ...

Phase 1 진행률: ███████░░░ 60%"
```

---

## Milestone Adaptation Rules

### Adding Milestones

- New user ideas become milestones added to the appropriate phase
- Maintain order: current phase milestones first, then next phase
- If a new idea conflicts with an existing milestone, resolve with the user

### Skipping Milestones

- If the user says "이거 필요 없을 것 같아" → mark as skipped, move on
- The AI may suggest skipping if a milestone doesn't fit the evolving design

### Reordering Milestones

- The AI may suggest reordering if a different sequence would be more fun to test
- Always get user confirmation before reordering: "이거 먼저 하면 더 재밌을 것 같은데, 순서 바꿔도 될까요?"

### Splitting Milestones

- If a milestone is taking too long (> 30 min), split it
- Present the split to the user: "이 기능이 좀 커서 둘로 나눌게요: [A] 먼저, 그 다음 [B]"

---

## Session Boundary Handling

### End of Session

- Always end on a completed milestone (never mid-implementation)
- If time is short, finish current milestone and defer the next
- Update `GAME_STATUS.md` with current progress
- Commit with milestone-based message

### Start of Session

- Show progress tracker
- Suggest next milestone: "오늘은 [다음 마일스톤]부터 시작하면 될 것 같아요!"
- Ask if the user has new ideas or wants to continue the plan

---

## First Session Goals

The first session MUST accomplish:

1. Environment setup (onboarding.md)
2. Game concept interview (game-design-interview.md) — can be brief if user has clear vision
3. At minimum, Milestones 1-3 of the chosen track (something moves on screen)
4. Ideally, Milestone 5 (core mechanic working)

**Target**: User leaves first session with a playable prototype and excitement to continue.
