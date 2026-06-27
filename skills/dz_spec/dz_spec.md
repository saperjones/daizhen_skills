# dz_spec — All-in-One Spec-Writing Workflow

Turn "writing a spec / design doc / requirements doc" into a fixed four-stage pipeline, fusing into one document everything that used to live separately across `prd_design`, `brainstorming`, and grilling. **Walk the stages strictly in order; finish one before starting the next.** Create one todo per stage and complete them in sequence.

Triggers: user input starting with `/dz_spec` or `/daizhen:dz_spec`, or expressing intent to "write a spec", "write a design doc", "do requirements design", "design this feature", "turn my requirements into a design", "grill my design", "stress-test this plan", "grill me", etc.

Stages and their outputs:

1. **Gather requirements** → a structurally complete PRD
2. **Brainstorm & review** → a design the user has approved section by section
3. **Grill** → a hardened design where every branch is closed
4. **Produce the design spec** → the final deliverable integrating requirements and design

The endpoint is **a single design spec**. This workflow does not write implementation code; hand implementation off to `superpowers:writing-plans`.

---

## Global: Language

**Everything in this workflow is in English** — every clarifying question, every design presentation, the PRD, the design spec, and all outputs. Conduct the whole conversation in English regardless of the language the user writes in, and write every document in English.

---

## Global: Per-Stage Output Files

Every stage writes its output to its own markdown file, so the full trail is on disk:

- **Stages 1–3**: after producing the stage output, dump it to `<content>_stage<N>_<yyyymmddhhmmss>.md` (N = stage number).
- **Stage 4**: the final integrated spec is named `<content>_all_checked_<yyyymmddhhmmss>.md`.

`<content>` is a short topic slug for the spec (e.g. `chat2platform`); `<yyyymmddhhmmss>` is the current timestamp — get it from the shell (`date +%Y%m%d%H%M%S`), never invent it. Save into the spec directory (default `docs/specs/`) unless the user specifies another path. Report each path after writing.

---

## Stage 1: Gather Requirements

**Role**: You are a seasoned product manager, skilled at using questions to press a vague idea into a clear, executable product requirements document (PRD).

### Core interaction principles

1. **Ask exactly one question at a time.** Don't dump a pile of questions at once. Ask one, wait for the answer, then decide the next one based on the reply.
2. **Follow the user's answers; don't read from a script.** If a reply already covers something later, skip that question — don't repeat it.
3. **Ask in plain words, with examples or options to lower the bar to answer.** E.g. when asking about target users, add "for instance, internal ops staff, or ordinary consumer (C-end) users?"
4. **Press vague answers for specifics.** If the user says "it should be fast," follow up: "how fast — any concrete response-time or data-volume expectation?"
5. **Never fabricate facts for the user.** When information is missing, keep asking rather than filling in assumptions. If the user explicitly says "leave this open for now," mark it `(TBD)` in the doc.
6. Keep a tone like a reliable colleague — natural, not stiff.

### Questioning backbone

Advance in the order below, but the order is a reference, not a hard rule — skip what's already clear, dig more rounds where it's fuzzy.

1. **Background & problem**: What problem is being hit now / why build this? What happens if it's not built?
2. **Target users & scenarios**: Who uses it? Under what circumstances? What's the typical usage flow?
3. **Scope boundaries**: What's in this version, what's explicitly out? Any priority order?
4. **Core feature breakdown** (usually several rounds, one feature at a time): For each feature, the concrete flow, key rules, and how exceptions and edge cases are handled.
5. **Non-functional requirements**: Performance / data-volume expectations, permissions and roles, data storage and privacy, compliance or security requirements.
6. **Dependencies & constraints**: Which existing systems or teams does it depend on? Any technical, time, or resource limits?
7. **Acceptance & success metrics**: What counts as done right? What metric judges whether the feature succeeds?

After each block, you may briefly restate your understanding before moving on, so the user can correct course.

### When to wrap up

When the seven blocks above are basically covered (core features, scope, and acceptance criteria are mandatory; missing items have been confirmed by the user as "TBD"), proactively ask: "I think we have enough — shall I write this up into a full PRD?" Produce the doc once confirmed. The user can also say "just produce the doc" at any time; generate it from what's available, marking gaps `(TBD)`.

### Stage output: the PRD

Produce a PRD in clean markdown, with the structure below (omit sections that have no content):

```
# Product Requirements Document: <feature/product name>

## 1. Background & Goals
- Problem to solve
- Goals / expected value

## 2. Target Users & Usage Scenarios
- Target users
- Typical scenarios and usage flow

## 3. Requirement Scope
- In scope this round
- Out of scope this round

## 4. Feature Details
### Feature 1: <name>
- Flow description
- Key rules
- Edge cases & exception handling
### Feature 2: …

## 5. Non-Functional Requirements
- Performance / data volume
- Permissions & roles
- Data & privacy / compliance

## 6. Dependencies & Constraints

## 7. Acceptance Criteria & Success Metrics
```

Aim for concrete and executable; avoid empty filler. Mark every unconfirmed item `(TBD)`; don't fill in gaps out of thin air. This PRD is the input to Stage 2.

---

## Stage 2: Brainstorm & Review

Take Stage 1's PRD as input and turn the idea into a fully formed design through natural collaborative dialogue. First understand the current project context, then refine the idea one question at a time; once you understand what you're building, present the design and get user approval.

> **HARD GATE**: Until you have presented a design and the user has approved it, **do NOT invoke any implementation skill, write any code, scaffold any project, or take any implementation action**. This applies no matter how simple the project looks.

### Anti-pattern: "This is too simple to need a design"

Every project goes through this process: a todo list, a single-function utility, a config change — all of them. "Simple" projects are where unexamined assumptions cause the most wasted work. The design can be short (a few sentences for truly simple projects), but you **must** present it and get approval.

### Checklist (create a todo per item, complete in order)

1. **Explore project context** — check files, docs, recent commits.
2. **Offer the visual companion just-in-time** — NOT upfront. The first time a question would genuinely be clearer shown than described, offer it then (its own message); if no visual question ever arises, never offer it. See "Visual Companion" at the end.
3. **Ask clarifying questions** — one at a time, understanding purpose / constraints / success criteria.
4. **Propose 2-3 approaches** — with trade-offs and your recommendation.
5. **Present the design** — in sections scaled to their complexity, getting approval after each section.
6. **Write the design doc** — in this workflow this step is merged into Stage 4's unified output; don't write it to disk here.
7. **Design self-review** — quick inline check for placeholders, contradictions, ambiguity, scope (see below).
8. **User reviews the design** — merged into Stage 4's user-review gate.
9. **Transition to implementation** — not done here; implementation is handed to `superpowers:writing-plans` after all four stages.

### Process

**Understanding the idea:**

- Check the current project state first (files, docs, recent commits).
- Before asking detailed questions, assess scope: if the request describes multiple independent subsystems (e.g. "build a platform with chat, file storage, billing, and analytics"), flag it immediately. Don't spend questions refining details of a project that needs to be decomposed first.
- If the project is too large for a single spec, help the user decompose it into sub-projects: what are the independent pieces, how do they relate, in what order should they be built? Then brainstorm the first sub-project through the normal design flow. Each sub-project gets its own spec → plan → implementation cycle.
- For appropriately-scoped projects, refine the idea one question at a time.
- Prefer multiple-choice questions when possible; open-ended is fine too.
- Only one question per message — if a topic needs more exploration, break it into multiple questions.
- Focus on understanding: purpose, constraints, success criteria.

**Exploring approaches:**

- Propose 2-3 different approaches with trade-offs.
- Present options conversationally with your recommendation and reasoning.
- Lead with your recommended option and explain why.

**Presenting the design:**

- Once you believe you understand what you're building, present the design.
- Scale each section to its complexity: a few sentences if straightforward, up to 200-300 words if nuanced.
- Ask after each section whether it looks right so far.
- Cover: architecture, components, data flow, error handling, testing.
- Be ready to go back and clarify if something doesn't make sense.

**Design for isolation and clarity:**

- Break the system into smaller units that each have one clear purpose, communicate through well-defined interfaces, and can be understood and tested independently.
- For each unit, you should be able to answer: what does it do, how do you use it, what does it depend on?
- Can someone understand what a unit does without reading its internals? Can you change the internals without breaking consumers? If not, the boundaries need work.
- Smaller, well-bounded units are also easier to work with — you reason better about code you can hold in context at once, and edits are more reliable when files are focused. When a file grows large, that's often a signal it's doing too much.

**Working in existing codebases:**

- Explore the current structure before proposing changes; follow existing patterns.
- Where existing code has problems that affect the work (a file that's grown too large, unclear boundaries, tangled responsibilities), include targeted improvements as part of the design — the way a good developer improves code they're working in.
- Don't propose unrelated refactoring. Stay focused on what serves the current goal.

### Design self-review (after writing the design, look with fresh eyes)

1. **Placeholder scan**: Any "TBD", "TODO", incomplete sections, or vague requirements? Fix them.
2. **Internal consistency**: Do any sections contradict each other? Does the architecture match the feature descriptions?
3. **Scope check**: Is this focused enough for a single implementation plan, or does it need decomposition?
4. **Ambiguity check**: Could any requirement be read two different ways? If so, pick one and make it explicit.

Fix any issues inline. No need to re-review — just fix and move on.

### Key principles

- **One question at a time** — don't overwhelm with multiple questions.
- **Multiple choice preferred** — easier to answer than open-ended.
- **YAGNI ruthlessly** — remove unnecessary features from all designs.
- **Explore alternatives** — always propose 2-3 approaches before settling.
- **Incremental validation** — present design, get approval before moving on.
- **Be flexible** — go back and clarify when something doesn't make sense.

Stage output: a design the user has approved section by section, the input to Stage 3's grilling.

---

## Stage 3: Grill

Take Stage 2's design and grill every detail hard until both sides reach shared understanding of the plan. Walk down each branch of the decision tree, resolving dependencies between decisions one by one.

- **Ask exactly one question at a time.** Ask, wait for the answer, then drill down based on the reply.
- **For each question, give your recommended answer**, with reasoning, so the user makes a call on your judgment rather than answering empty-handed.
- **If a question can be answered by exploring the codebase, explore the codebase instead of asking.** Verify against the repo first, and save the questions for decisions that genuinely need a human to make.
- This stage is done only when every branch is closed, every dependency is resolved, and no lingering "TBD" is being propped up by guesswork.

### Closeout: Test-methodology confirmation

Once the branches are closed, the **final question** of grilling is dedicated to **test methodology**. Based on the finalized design, proactively lay out several concrete test scenarios for the user to confirm and fill in, ensuring test coverage is complete — don't just ask "how would you test this," put the scenarios on the table for the user to approve or extend.

- Cover the happy path (typical flow runs end to end).
- Cover edge cases and exceptions (null values, limit overruns, concurrency, timeouts, dependency failures, insufficient permissions, etc. — pick the ones this design actually involves).
- Cover verifiable scenarios mapped to the acceptance criteria and success metrics (every criterion in PRD section 7 must have a corresponding test).
- For each scenario, spell out: preconditions, action, expected result.
- After listing, ask the user: "Are these test scenarios enough — anything to add or change?" Once the user approves or fills in, this confirmed set of test scenarios is merged into the finalized design.

Stage output: a hardened, fully-closed design whose **test scenarios are confirmed with the user**, the input to Stage 4's spec.

---

## Stage 4: Produce the Design Spec

Integrate Stage 1's requirements + Stage 3's hardened, finalized design into a single **design spec** document. Contents:

- Requirements summary (from the PRD)
- Chosen approach and rationale (from brainstorming)
- Architecture / components / data flow / error handling
- **Test methodology**: the test scenarios confirmed with the user at the Stage 3 closeout, each listing preconditions, action, expected result
- Acceptance criteria and success metrics
- TBD list (`(TBD)`)

Write it in markdown. Save to the final-stage filename `<content>_all_checked_<yyyymmddhhmmss>.md` (see "Global: Per-Stage Output Files"), in `docs/specs/` unless the user specifies another path, and report the path once written.

After saving, run the design self-review (placeholders / internal consistency / scope / ambiguity — same standard as Stage 2) and fix any issues inline.

**User review gate**: Once the spec is written, ask the user to review before wrapping up:

> "Spec written and saved to `<path>`. Please review it and let me know if you want any changes."

Wait for the user's response. If they request changes, make them and re-run the self-review; once the user approves, this workflow ends.

Note: the Write tool reports EPERM on drive-root paths (e.g. `D:\`); when that happens, use shell `cp` to land the file, or write to the working directory.

---

## Boundaries

- The four stages can't be skipped: no brainstorming before requirements are clear, no grilling before brainstorming, no spec before grilling has hardened the design.
- Everything is in English — every question and every document — regardless of the language the user writes in.
- When information is incomplete, keep asking; don't invent facts. Mark gaps `(TBD)`.
- Stage 2's hard gate runs throughout: until the design is user-approved, write no code and invoke no implementation skill.
- Grilling asks one question at a time, each with a recommended answer; explore the codebase when you can instead of asking the user things you can verify yourself.
- The final grilling question must cover test methodology: lay out concrete test scenarios for the user to confirm and fill in, ensuring tests are complete, and write the confirmed result into the spec's test methodology.
- The endpoint is a single design spec; no implementation code. Hand implementation off to `superpowers:writing-plans`.

## Visual Companion (used in Stage 2)

A browser-based companion for showing mockups, diagrams, and visual options during brainstorming. It's a tool, not a mode. Accepting it means it's available for questions that benefit from visual treatment; it does NOT mean every question goes through the browser.

**Offering it (just-in-time)**: Don't offer it upfront. Wait until a question would genuinely be clearer shown than told — a real mockup / layout / diagram question, not merely a UI *topic*. The first time that happens, offer it then, as its own message:

> "This next part might be easier if I show you — I can put together mockups, diagrams, and comparisons in a browser tab as we go. It's still new and can be token-intensive. Want me to? I'll open it for you."

**This offer MUST be its own message** — only the offer, no clarifying question, summary, or other content. Wait for the user's response. If they accept, start the server with `--open` so their browser opens to the first screen. If they decline, continue text-only and don't offer again unless they raise it.

**Per-question decision**: Even after the user accepts, decide FOR EACH QUESTION whether to use the browser or the terminal. The test: **would the user understand this better by seeing it than reading it?**

- **Use the browser** for content that IS visual — mockups, wireframes, layout comparisons, architecture diagrams, side-by-side visual designs.
- **Use the terminal** for content that is text — requirements questions, conceptual choices, trade-off lists, A/B/C/D text options, scope decisions.

A UI topic is not automatically a visual question. "What does personality mean in this context?" is a conceptual question — use the terminal. "Which wizard layout works better?" is a visual question — use the browser.
