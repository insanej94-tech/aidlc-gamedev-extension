# Game Development Orchestration — Master Workflow

## Overview

This is the **master control document** for the Game Development Extension. It defines the exact sequence in which the AI loads files, makes decisions, and progresses through the game development lifecycle.

**When to Load This File**: ALWAYS. This file is loaded FIRST when the Game Development extension is enabled. All other files are loaded on-demand based on the triggers defined here.

**Purpose**: Eliminate ambiguity about what to do, when to do it, and which reference to consult at each stage.

---

## File Registry

### Core Files (Always Loaded at Extension Start)

| File | Purpose | When Referenced |
|---|---|---|
| `ORCHESTRATION.md` | This file — master workflow control | Always active |
| `gamedev-baseline.opt-in.md` | Extension activation prompt | Phase 0 — opt-in decision |
| `gamedev-baseline.md` | 10 GAMEDEV rules (enforcement) | Every stage — compliance check |
| `non-developer-mode.md` | Behavioral protocol | Every user interaction |

### Phase-Specific Files (Loaded On-Demand)

| File | Loaded When | Purpose |
|---|---|---|
| `onboarding.md` | New user OR no Godot project detected | Environment setup |
| `game-design-interview.md` | No game concept defined yet | Ideation and concept |
| `godot-puzzle-patterns.md` | During any code implementation | Technical reference (AI-only) |
| `milestone-progression.md` | After concept confirmed | Development planning |
| `asset-management.md` | When creating/upgrading visuals or audio | Asset decisions |
| `fun-design-framework.md` | When designing mechanics or fixing "not fun" | Fun engineering |
| `ai-opponent-design.md` | When implementing AI enemies/opponents | Opponent behavior |
| `playtesting-loop.md` | After every milestone completion | Feedback collection |
| `error-handling-for-beginners.md` | When errors occur | Error management |
| `version-control-guide.md` | At commit/push points | Git automation |
| `in-game-tutorial.md` | When building first-play experience | Player onboarding |
| `accessibility.md` | During implementation of any feature | Inclusive design |
| `game-export-guide.md` | When user wants to share/distribute | Export and distribution |
| `post-completion-guide.md` | When game reaches "playable" state | Release and maintenance |
| `multiplayer-architecture.md` | When user requests multiplayer | Network play |
| `localization.md` | When user wants multi-language support | Translation |

---

## Master Workflow Sequence

### Phase 0: Initialization

```text
TRIGGER: Game Development extension enabled (opt-in answer A or B)

ACTIONS:
1. Load ORCHESTRATION.md (this file)
2. Load gamedev-baseline.md (rules enforcement)
3. Load non-developer-mode.md (behavioral protocol)
4. Check: Does a GAME_STATUS.md exist in workspace?
   → YES: Go to Phase R (Resume)
   → NO: Go to Phase 1 (Onboarding)
```

### Phase R: Resume Existing Session

```text
TRIGGER: GAME_STATUS.md found

ACTIONS:
1. Read GAME_STATUS.md
2. Present session summary (per non-developer-mode.md session flow)
3. Determine current phase from progress data
4. Resume at appropriate phase below
```

### Phase 1: Onboarding

```text
TRIGGER: New user, no project exists

LOAD: onboarding.md

ACTIONS:
1. Check Godot installation
2. Guide setup if needed
3. Create project structure
4. Verify first run works
5. Setup GitHub (if user wants)
6. Create GAME_STATUS.md

EXIT: When project runs successfully → Phase 2
```

### Phase 2: Game Design

```text
TRIGGER: Project exists but no game concept defined

LOAD: game-design-interview.md, fun-design-framework.md

ACTIONS:
1. Conduct design interview (5 stages max)
2. Apply fun-design-framework principles to concept
3. Generate concept summary
4. Present first 5 milestones
5. Get user confirmation

EXIT: When user confirms concept → Phase 3

SHORTCUT: If user comes with clear vision, skip to Phase 3 immediately
```

### Phase 3: Construction (Main Development Loop)

```text
TRIGGER: Game concept confirmed

LOAD: milestone-progression.md, godot-puzzle-patterns.md, accessibility.md

ACTIONS (per milestone):
┌─────────────────────────────────────────────────────────┐
│ 3a. Announce milestone start                            │
│ 3b. Implement (consult godot-puzzle-patterns.md)        │
│ 3c. Apply accessibility rules (accessibility.md)        │
│ 3d. Apply juice/fun (fun-design-framework.md)           │
│ 3e. Handle errors silently (error-handling-for-beginners│
│ 3f. Auto-commit (version-control-guide.md)              │
│ 3g. Present completion + playtest prompt                │
│     (playtesting-loop.md)                               │
│ 3h. Collect feedback, iterate                           │
│ 3i. Move to next milestone                              │
└─────────────────────────────────────────────────────────┘

CONDITIONAL LOADS DURING CONSTRUCTION:
- User asks for AI opponent → load ai-opponent-design.md
- Visuals/audio needed → load asset-management.md
- User asks for multiplayer → load multiplayer-architecture.md
- "First play" experience needed → load in-game-tutorial.md

EXIT: When user is satisfied OR all planned milestones complete → Phase 4
```

### Phase 4: Polish and Export

```text
TRIGGER: Core features complete, user wants to share

LOAD: post-completion-guide.md, game-export-guide.md

ACTIONS:
1. Run polish checklist (post-completion-guide.md)
2. Implement polish items proactively
3. Suggest export when ready
4. Handle export process (game-export-guide.md)
5. Guide distribution (itch.io, etc.)

EXIT: When game is published → Phase 5
```

### Phase 5: Maintenance and Growth

```text
TRIGGER: Game is live/shared

LOAD: post-completion-guide.md (maintenance section)

ACTIONS:
1. Collect external feedback
2. Suggest updates based on feedback
3. Implement fixes and features per user direction
4. Manage versions and releases
5. Suggest expansion or new project when appropriate

CONDITIONAL:
- User wants multi-language → load localization.md
- User wants online multiplayer → load multiplayer-architecture.md
```

---

## Decision Flowchart

```text
User says something
       │
       ▼
Is it a game DESIGN decision?
       │
  YES ──┼── NO
  │     │     │
  │     │     ▼
  │     │  Is it a TECHNICAL decision?
  │     │     │
  │     │  YES ──→ AI decides silently (GAMEDEV-01)
  │     │     │
  │     │     ▼
  │     │  Is it a FEELING/FEEDBACK?
  │     │     │
  │     │  YES ──→ Interpret and act (non-developer-mode.md)
  │     │     │
  │     │     ▼
  │     │  Is it a REQUEST for new feature?
  │     │     │
  │     │  YES ──→ Add to milestones or implement now (scope check)
  │     │
  │     ▼
  │  Present clear options to user (Category 3 from non-developer-mode.md)
  │
  ▼
Record decision in GAME_STATUS.md
```

---

## Cross-Cutting Concerns (Apply at Every Stage)

### At Every User Interaction

- [ ] Language: Plain Korean, no technical terms (GAMEDEV-02)
- [ ] Tone: Friendly, encouraging, concise (non-developer-mode.md)
- [ ] Proactivity: Anticipate needs, do more than asked (GAMEDEV-07)

### At Every Implementation

- [ ] Accessibility: Color-blind safe, keyboard navigable (accessibility.md)
- [ ] Fun: Juice applied, feel checked (fun-design-framework.md)
- [ ] Playable: Game runs after every change (GAMEDEV-03)
- [ ] Saved: Auto-commit at natural points (GAMEDEV-05)

### At Every Milestone Completion

- [ ] Playtest prompt delivered (playtesting-loop.md)
- [ ] Progress tracker updated (milestone-progression.md)
- [ ] GAME_STATUS.md updated (version-control-guide.md)
- [ ] Compliance summary (if full mode): GAMEDEV-01 through GAMEDEV-10

---

## File Dependency Graph

```text
ORCHESTRATION.md (root — controls everything)
    │
    ├── gamedev-baseline.md (rules — always active)
    │
    ├── non-developer-mode.md (behavior — always active)
    │
    ├── onboarding.md (Phase 1)
    │       └── references: version-control-guide.md (GitHub setup)
    │
    ├── game-design-interview.md (Phase 2)
    │       └── references: fun-design-framework.md (design principles)
    │
    ├── milestone-progression.md (Phase 3 — planning)
    │       └── references: godot-puzzle-patterns.md (implementation)
    │                        playtesting-loop.md (feedback)
    │                        error-handling-for-beginners.md (issues)
    │                        version-control-guide.md (saving)
    │                        accessibility.md (inclusive design)
    │
    ├── asset-management.md (Phase 3 — on demand)
    ├── fun-design-framework.md (Phase 2-3 — design + polish)
    ├── ai-opponent-design.md (Phase 3 — on demand)
    ├── in-game-tutorial.md (Phase 3 — on demand)
    ├── multiplayer-architecture.md (Phase 3-5 — on demand)
    │
    ├── game-export-guide.md (Phase 4)
    ├── post-completion-guide.md (Phase 4-5)
    │
    └── localization.md (Phase 5 — on demand)
```

---

## Context Efficiency Rules

### Minimize Context Usage

To keep the AI's context window efficient:

1. **Load only what's needed** — follow the triggers above, don't pre-load everything
2. **Unload after use** — once onboarding is done, that file's rules are internalized
3. **Reference, don't repeat** — files should not duplicate each other's content
4. **GAME_STATUS.md is the memory** — not the AI's context window
5. **Authoritative Source rule** — when content appears in multiple files, the designated source below is canonical

### Authoritative Source Table (Resolves Duplication)

When the same concept appears in multiple files, the designated source is the single truth:

| Topic | Authoritative Source | Other Files Defer To It |
|---|---|---|
| Feedback interpretation (user says → AI does) | `non-developer-mode.md` | `playtesting-loop.md` references it, does not repeat |
| Milestone completion template | `milestone-progression.md` | `playtesting-loop.md`, `non-developer-mode.md` reference it |
| Juice/polish checklist | `fun-design-framework.md` | `godot-puzzle-patterns.md`, `post-completion-guide.md` reference it |
| Sound design (action → sound mapping) | `asset-management.md` | `fun-design-framework.md` references it |
| Accessibility rules (color, input, vision) | `accessibility.md` | `godot-puzzle-patterns.md` references it |
| Difficulty/speed settings for player | `accessibility.md` GAMEDEV-A6 | `fun-design-framework.md` references it |
| AI opponent difficulty levels | `ai-opponent-design.md` | Other files reference it |
| Session start/resume protocol | `ORCHESTRATION.md` Phase R | `non-developer-mode.md`, `version-control-guide.md` follow it |
| Git commit strategy | `version-control-guide.md` | `gamedev-baseline.md` GAMEDEV-05 summarizes it |
| Error recovery with rollback | `version-control-guide.md` | `error-handling-for-beginners.md` references it |

**Rule**: If a conflicting statement exists between two files, the authoritative source wins.

### Priority When Context is Limited

If context space is constrained, prioritize in this order:

1. `ORCHESTRATION.md` (navigation)
2. `gamedev-baseline.md` (rules)
3. `non-developer-mode.md` (behavior)
4. Current phase's primary file
5. `godot-puzzle-patterns.md` (implementation reference)
6. Everything else (load as needed)

---

## Error Recovery

### If AI Loses Context (Session Break / Compaction)

```text
Recovery procedure:
1. Read GAME_STATUS.md
2. Load ORCHESTRATION.md
3. Determine current phase
4. Load phase-appropriate files
5. Resume where left off
6. Brief user summary: "지난번에 [X]까지 했었죠!"
```

### If Project State is Inconsistent

```text
1. Check git log for last known good state
2. Verify project runs (F5 test)
3. If broken: revert to last milestone commit
4. If fine: continue from current state
5. Never alarm the user — just fix and continue
```

---

## Success Metrics

The workflow is successful when:

- [ ] User has a playable game within first session
- [ ] User never encounters raw technical language
- [ ] User never feels stuck or confused
- [ ] Every session ends with visible progress
- [ ] The game is fun (user wants to keep playing their own game)
- [ ] User can share the game with friends
- [ ] All decisions are preserved across sessions
