# Run A — Execution plan

Plan for the Claude Code path redesign of the Agent Profile Dashboard. Grounded in the updated brief + rubric (baseline `53db6095`) and the discovery document (`run-a-discovery.md`).

This is a plan for your review, not a committed path. Pause for explicit approval before Phase 2 begins.

---

## Shape of the work

Five phases. Phase 0 is done; Phase 1 is the next block of work and produces a paper design you approve before any TypeScript changes. Phase 2 lands the structural frame. Phase 3 redesigns the modules inside that frame. Phase 4 polishes, writes run-notes, and self-scores against the rubric.

Four mandatory checkpoints — one before each phase that mutates code, plus a final one before I call the run done.

| Phase | Artifact | Rough size | Opens | Closes |
|---|---|---|---|---|
| 0 | Discovery (done) | n/a | — | this-plan checkpoint |
| 1 | Concept sketch (markdown, no code) | 2–3 hours | concept checkpoint | concept-approved |
| 2 | Structural implementation in `AgentDetail.tsx` | 3–5 hours | structure checkpoint | structure-approved |
| 3 | Module-level redesign | 5–8 hours, iterated per module | module-level checkpoints (×4) | polish-ready |
| 4 | Polish + `run-a-notes.md` + rubric self-check | 2–3 hours | final checkpoint | run-a-complete |

Sizes are my best guesses. A big design pivot in Phase 1 can redraw the timeline for 2–3.

---

## Phase 1 — Concept (paper only)

**Deliverable:** `projects/agent-profile-dashboard/run-a-concept.md`. Not code.

### What the concept answers

1. **Hero frame** — what goes in the monitoring-no-scroll 1440px band? Candidates: (a) current activity state + current run/issue + recent health pills + budget position, all in one dense horizontal strip; (b) a single hero card that changes shape based on live/idle; (c) a split — "now" on the left, "budget position" on the right. Pick one, explain tradeoffs.
2. **Chart consolidation strategy.** Four charts today; the brief calls them uneven value. Options: keep 4 (fail monitoring goal), drop 2 and merge 2 (most likely), consolidate to 1 richer chart, hide behind a tab/accordion. Paper-sketch the replacement surface and name which charts survive.
3. **Budget position placement.** Embed in hero (Section 1 item 4 requires no-scroll visibility). Decide shape — progress bar, utilization %, `amount / limit` pair, or combination. Draws from `BudgetPolicyCard` existing treatment for consistency.
4. **Change-priority affordance.** Two concrete options already in the brief (drag between buckets vs. explicit selector); I'll recommend one in the concept with reasoning, based on DS precedent (`KanbanBoard.tsx` uses drag for status changes — drag-across-buckets for priority is the mirror pattern).
5. **Session burn rate placement.** Brief says "may live in a secondary surface" — propose how it integrates with the redesigned costs section. Not required in monitoring-no-scroll band.
6. **Live-run signal treatment.** Preserve cyan or propose a replacement. I'm inclined to keep cyan because it's the canonical "this is alive" signal across the app (header mobile indicator, workspace-operation status); replacing it introduces inconsistency. Concept defends this.
7. **Recent health representation.** Today it's implicit in the charts. Section 1 item 3 requires it visible in monitoring frame. Probably 3–5 status dots or a compact run strip showing the most recent N runs with their outcomes. Sketch which.

### What the concept does NOT do

No chart tokenization. No component extraction. No new tokens. No changes outside `AgentOverview` / `LatestRunCard` / `CostsSection`. No edits to `status-colors.ts` or DS files.

### Checkpoint — concept review

I pause after the concept is written. You approve, revise, or reject. No code has been written at this point so revisions are cheap.

---

## Phase 2 — Structural implementation

Small, reviewable, and *structural only*. I turn the approved concept into working TypeScript that you can see in the browser. Module styling is deliberately minimal in this phase — the goal is to prove the frame fits the brief before investing in the visual pass.

### What lands

1. **Prop-passing change:** thread `agentBudgetSummary` (already computed in `AgentDetail`) into `AgentOverview` as a new optional prop. One-line callsite edit + typed prop.
2. **Top-level layout rewrite** of `AgentOverview` to match the approved concept (hero band + secondary bands).
3. **Stub-level module edits:** `LatestRunCard`, charts, priority-change, costs each rendered in their approved positions but with current styling carried over mostly intact. Deliberately unfinished — the module-redesign pass in Phase 3 is where the visual work happens.
4. **Monitoring-frame smoke test:** load the dashboard at 1440px viewport with a seeded dataset (live run + idle agent) and confirm no-scroll-required pass by measurement, not feel.

### What does not land in this phase

- No chart merges / consolidation (those are module-level work for Phase 3).
- No new visual affordances for priority change — just a placeholder (e.g., a disabled `Change priority →` link) to reserve space.
- No polish, no copy, no final typography.

### Checkpoint — structure review

You open the running dashboard in a browser and confirm the frame reads the way the concept promised. Crucially: does the 1440px no-scroll test pass? If not, this is the cheap moment to fix it before modules are styled. Revisions here cascade cheaply; after Phase 3 they're expensive.

---

## Phase 3 — Module-level redesign

Four modules, iterated in dependency order. Each module gets its own mini-checkpoint so we don't have to unwind a pile of work if a module doesn't land.

### 3a. Hero (live/latest run + activity + recent health + budget position) — ~2 hours

- Redesigns what was `LatestRunCard` plus new budget position + recent-health elements.
- Live-run visual treatment nailed down (cyan pulse preserved or approved replacement).
- Budget position rendered; decide on `BudgetPolicyCard` reuse vs. inlined variant.
- Recent-health dots / strip rendered from `heartbeats` array.
- **Sub-checkpoint:** hero is the load-bearing surface for Section 1. Review after it lands.

### 3b. Charts (consolidation executed) — ~1.5 hours

- Whatever the concept decided: merge, drop, or pull behind secondary view.
- Tokens remain hardcoded hex (Section 5 DS compliance: no chart tokenization).
- Empty states reviewed — the `rounded-sm` placeholder (`ActivityCharts.tsx:113`) in-scope only if chart usage changes.
- **Sub-checkpoint:** charts merged as planned.

### 3c. Priority-change affordance (on the Recent Issues surface, or wherever concept placed it) — ~2 hours

- If drag: reuse `@dnd-kit` pattern from `KanbanBoard.tsx`. Four-bucket `SortableContext`s.
- If explicit selector: dropdown or segmented control per row.
- Wires to `issuesApi.update(id, { priority })`. Optimistic update + query invalidation for `queryKeys.issues.list`.
- Keyboard path: if drag is the primary affordance, keyboard fallback is mandatory for rubric item "drag-and-drop or keyboard-reorderable." `@dnd-kit` supports keyboard sensors.
- **Sub-checkpoint:** affordance works end-to-end.

### 3d. Costs (unified, with session burn rate integrated) — ~1.5 hours

- Merge KPI strip + per-run table into one coherent surface.
- Session burn rate computed from `runtimeState.totalCostCents` or derived per-run; rendered as a secondary signal inside this module (not in monitoring frame).
- **Sub-checkpoint:** costs module works.

Between sub-checkpoints I only report briefly; you greenlight each before I move on.

---

## Phase 4 — Polish, run-notes, rubric self-check

### What lands

1. **Visual restraint pass.** Typography, spacing, density. No decorative additions. Match Step 0 DS policy aggressively. Section 6 "Visual restraint" is scored here.
2. **Accessibility audit.** Keyboard navigation through priority-change affordance, hover states, focus rings, aria labels where needed.
3. **`projects/agent-profile-dashboard/run-a-notes.md`** with:
   - DS deviations and their justifications (rubric Section 5 is "pass with explicit justification" — this file is the justification channel).
   - Implicit decisions made during the run: how I defined "in-flight tasks," chart consolidation choices, live-run signal choice, session-burn-rate placement.
   - Header-is-source-of-truth-for-agent-level-controls note (paused state), per your spirit interpretation decision.
   - Known tensions / deferred items.
4. **Rubric self-check.** Walk each rubric section and mark pass/fail with one-sentence reasoning, per rubric instructions. Section 1 and Section 3 "change task priority" are the items most at risk.

### Final checkpoint

I present the running dashboard, the run-notes, and the self-scored rubric. You review and decide: accept, revise, or redo a phase.

---

## Risk areas

Ordered by how likely they are to bite, not by impact.

1. **Fit-without-scrolling on 1440px is the single biggest technical risk.** Budget position, recent health, current work, and activity state all in the monitoring band puts pressure on vertical space. If the hero band ends up 200px+ tall, there won't be room for anything else above the fold. Mitigation: bail out of chart consolidation (push charts below the fold entirely) before we compromise on hero content.
2. **"In-flight tasks" is still squishy.** Even with the priority-change reframe, the set of issues the dashboard targets has to be defined. Candidate: issues with status `todo` / `in_progress` / `blocked` assigned to this agent. I'll propose a concrete filter in the concept; you approve before I write code against it.
3. **Priority drag across 4 buckets could feel heavy.** Dragging a card from the bottom of "medium" to "critical" is a big gesture for a 4-bucket enum. Mitigation: prototype the segmented-control alternative in the concept so you can pick which one to ship. This is concretely a brief-intent-ambiguity risk — the brief says "drag-and-drop **between priority buckets** or explicit priority selector," so both are permitted.
4. **Chart consolidation could drop a chart someone loves.** The `Issues by Priority` and `Issues by Status` charts are reachable-elsewhere candidates (they might duplicate the Issues list view). `Run Activity` + `Success Rate` are harder to drop — they're uniquely dashboard-shaped. Mitigation: the concept names which charts survive, and I cross-check what the other surfaces cover before proposing drops.
5. **Session burn rate "live" semantic is underspecified.** The brief requires both metrics but only the per-run cost data in `heartbeats` gives useful recent-window granularity. `runtimeState.totalCostCents` is session-lifetime, not rate. I'll propose "last N runs rolling cost" in the concept and you can push back if that's not what you meant.
6. **Status-color routing.** The existing `runStatusIcons` map uses raw-palette tones inline. The redesign is tempted to route everything through `StatusBadge`/`StatusIcon` for DS consistency — but `StatusBadge` only covers the status values in its catalog; run statuses include `scheduled_retry` and `timed_out` which may or may not be in `status-colors.ts`. If they aren't, and I can't modify `status-colors.ts`, I may have to keep the inline raw-palette map. Flagging so it's not a surprise in Phase 4.
7. **Scope creep temptation: header-vs-dashboard.** The color-drift cyan-pulse issue (header uses `bg-blue-*`, inline card uses `bg-cyan-*`) is real and visible. Fixing requires editing the header. Out of scope for Run A. I'll note it in run-notes and not touch it.
8. **`rounded-sm` in `ActivityCharts.tsx:113`.** Pre-existing, inside a shared chart component I shouldn't modify if the charts are dropped. If the charts stay and this empty-state placeholder is removed because we take a different approach, the `rounded-sm` goes with it. If the charts stay and the placeholder stays, it's a noted-but-accepted pre-existing state.
9. **Stale DS-policy bullets in the brief about pause** (`## Design system policy` bullet on "Pause-agent uses neutral button styling" and the entire `## Known deferrals` section about `--signal-warning`). These now reference a scope the brief has removed. Not a run-A execution risk — just a cleanup item for a follow-up brief tightening pass. Flagging in case you want a tiny cleanup commit before Phase 1.

---

## What "done" looks like — rubric mapping

Each rubric section mapped to the phase that delivers it. Phases are cumulative — by the end of Phase 4, every item has been touched.

| Rubric section | Delivered by | Notes |
|---|---|---|
| Section 1, item 1 (activity state) | Phase 3a (hero) | Relies on `agent.status` being surfaced in-frame |
| Section 1, item 2 (current work) | Phase 3a (hero) | Live-run card gives this; idle state needs an empty treatment |
| Section 1, item 3 (recent health) | Phase 3a (hero) | New element, not in current dashboard |
| Section 1, item 4 (budget position in-frame) | Phase 2 (structural) + Phase 3a (hero styling) | This is the brief's new hard requirement |
| Section 2, Operations | Phase 3c (priority affordance) | The priority-change surface IS the operations-mode answer |
| Section 2, Accountability (spend + tokens) | Phase 3d (unified costs) | Per-run data lets engineers investigate |
| Section 2, Debug | Phase 3a (hero) + Phase 3d (costs table provides failed-run context) | Failed runs must be 1-interaction-reachable |
| Section 3, Change task priority | Phase 3c | Drag or selector — concept picks |
| Section 3, Navigate to detail | Phase 3a (hero link) + Phase 3c (issue rows) | Already working today; preserved |
| Section 4, Chart data reachable | Phase 3b | Concept decides "reachable where" — probably a link-out or expander |
| Section 4, Cost + token both present | Phase 3d | Unified costs module shows both |
| Section 4, Recent issues reachable | Phase 3c (priority rows) | The priority-change surface IS the recent-issues list |
| Section 5, Rounded corners | Phase 4 audit | Default sharp; any `rounded-lg`/`xl`/`md` flagged in run-notes |
| Section 5, No new tokens | Phase 4 audit | Confirmed by git diff; tokens file untouched |
| Section 5, No component extraction | Phase 4 audit | Confirmed by git diff; no new files under `ui/src/components/` |
| Section 5, No chart tokenization | Phase 3b | Deliberately preserved |
| Section 5, No raw-palette drift | Phase 4 audit | Confirmed by diff review against pre-existing raw palette |
| Section 5, Pause neutral | Phase 4 audit | Vacuous — pause is out of scope, so "pass" by absence |
| Section 5, Live-run visual distinction | Phase 3a | Concept decides cyan vs. replacement |
| Section 5, No out-of-scope changes | Phase 4 audit | Confirmed by git diff limited to `AgentDetail.tsx` dashboard region |
| Section 6, Improvement over current | Phase 4 self-score | Most important qualitative axis |
| Section 6, Hierarchy clarity | Phase 3a + Phase 4 restraint pass | Concept decides the three layers |
| Section 6, Visual restraint | Phase 4 polish | The Swiss-minimal scoring axis |
| Section 6, Live-run signal strength | Phase 3a | Scored against concept-chosen treatment |
| Section 7 (friction log, not scored) | Phase 4 `run-a-notes.md` | Captured throughout, organized at end |

---

## Open questions before Phase 1 begins

Not blockers; noting so Phase 1 isn't littered with these.

1. **Stale DS-policy brief bullets on pause** — want a tiny cleanup commit before Phase 1, or accept it and let run-notes handle it in Phase 4?
2. **Headless smoke test for 1440px no-scroll** — is it okay if I manually open a browser and measure, or do you want a Playwright-style automated check? Manual is much cheaper.
3. **Seeded dataset for testing** — do you have a specific agent I should point at, or should I rely on whatever dev data is in my local db?

---

## Summary

Phase 0 complete. Phase 1 is concept-only and opens at the next checkpoint. Phase 2 onward requires concept approval. The plan treats the hero band as the load-bearing surface for Section 1 pass; all other modules serve secondary modes without crowding the hero. The three biggest risks (1440px fit, in-flight-tasks definition, chart consolidation) get early decisions in Phase 1 before any TypeScript is written.

Waiting on your read.
