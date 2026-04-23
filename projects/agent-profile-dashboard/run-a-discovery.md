# Run A — Discovery (deeper pass)

Observed structure of the current Dashboard-tab implementation, the APIs that feed it, and a reality check on the three named action affordances. No design decisions yet — just what's on disk.

---

## Executive summary (surface these at the checkpoint)

Flagged in priority order; details further down.

1. **🔴 Scope blocker — persisted task reordering has no data model support.** No `order`, `rank`, or `position` field exists on `Issue`, `AgentTaskSession`, or any adjacent type. The only reorderable-ish dimension is `priority`, which is a closed 4-value enum (`critical|high|medium|low`). `KanbanBoard.tsx` already uses `@dnd-kit` but only persists *column* (status) changes on drop — within-column ordering is visual-only. The brief's "drag-and-drop or keyboard-reorderable list scoped to top 5–10 in-flight tasks" cannot be implemented with server persistence against the current API.
2. **🟡 "In-flight tasks" is itself underdefined.** The data model exposes three candidate concepts — heartbeat runs (historical point-in-time), assigned issues (have status/priority, no user-specific order), task sessions (adapter-side, no ordering). None is obviously "the list of things the agent will work on next." Design needs to pick one and own the interpretation.
3. **🟡 Pause is already fully wired in the page chrome.** `PauseResumeButton` + `agentsApi.pause/resume` render in the header at `AgentDetail.tsx:942`. It already satisfies the brief's "single-click, reversible via same button, no confirmation, neutral styling" spec. Open question: does the brief want it *duplicated* inside the dashboard, *moved* into the dashboard, or does "surface on the dashboard" count as already satisfied by the ever-present header?
4. **🟡 `budgetOverview` is alive, not dead, but bypasses `AgentOverview`.** It's fetched for any authenticated user (not gated on `needsDashboardData`) and consumed at page-root level into `agentBudgetSummary` (line 699–729). It is *not* threaded into `AgentOverview` props. Redesign can opt to thread it through — live budget state is genuinely useful for the primary-goal "live economics" signal.
5. **🟢 Cyan-pulse live signal has color drift across surfaces.** Inline `LatestRunCard` uses `bg-cyan-400`; the header's mobile-only live indicator uses `bg-blue-400/500`. Same concept, different hues, not coordinated. A redesign that preserves "visual distinction for live-running agents" should pick one and apply it consistently within the dashboard.

Items 1 and 2 together are the biggest open question. I don't want to pick an interpretation without your sign-off.

---

## 1. `LatestRunCard` (`AgentDetail.tsx:1170–1255`)

### Props
```ts
{ runs: HeartbeatRun[]; agentId: string }
```
`agentId` here is `canonicalAgentRef` (URL-shaped key), not the UUID.

### State and hooks
- No state. One `useMemo` (line 1187) to extract a 2–3-line summary from `run.resultJson.summary ?? result ?? error`, stripping markdown headers, list marks, code fences, blockquotes, and table rows; capped at 3 lines or 280 chars.

### Render structure
- Container wrapper (`div.space-y-3`)
  - Header row (h3 with conditional cyan-pulse dot + label, right-aligned link to run detail)
  - Body `Link` card (`block border rounded-none p-4 space-y-2`) — clicking anywhere opens the run
    - Metadata row: `StatusIcon` (from `runStatusIcons` lookup) + `StatusBadge` + 8-char run id + source-tag pill + right-aligned `relativeTime`
    - Conditional summary block (`MarkdownBody`, max-h-16, overflow-hidden)

### Styling mechanism
Inline Tailwind classes + `cn()` for conditional live-vs-idle border/shadow.

### External dependencies
- DS tokens: `border-border`, `text-muted-foreground`, `hover:bg-muted/50`, `hover:text-foreground`.
- Raw-palette drift: `bg-cyan-400`, `border-cyan-500/30`, `shadow-[0_0_12px_rgba(6,182,212,0.08)]` for live state; source-tag pill uses `bg-blue-100/text-blue-700` (timer), `bg-violet-100/text-violet-700` (assignment), `bg-cyan-100/text-cyan-700` (on_demand).
- Shadcn: none directly — uses `Link` from the local router shim and plain `<div>`s.
- Status primitives: `runStatusIcons` (local `AgentDetail.tsx:103`, hex-adjacent Tailwind tones keyed by status), `StatusBadge` (from `status-colors.ts` catalog), `sourceLabels` (local `AgentDetail.tsx:168`).
- Charts / content: `MarkdownBody` for the excerpt.

### Edge cases and conditionals
- Zero runs → early `return null`. (So the card completely disappears when the agent has no runs yet — there is no "idle, never run" state today.)
- No live run present → picks the most-recent run by `createdAt` and renders it as "Latest Run" with the neutral border.
- `summary` empty → the excerpt div is omitted (card is still rendered with just the metadata row).
- Agent status is **not** consulted here — `paused` agents still show a "Latest Run" with no indication the agent is currently paused.

---

## 2. `AgentOverview` (`AgentDetail.tsx:1259–1335`)

### Props
```ts
{
  agent: AgentDetailRecord;
  runs: HeartbeatRun[];
  assignedIssues: Array<{
    id: string;
    title: string;
    status: string;
    priority: string;
    identifier?: string | null;
    createdAt: Date;
  }>;
  runtimeState?: AgentRuntimeState;
  agentId: string;           // UUID
  agentRouteId: string;      // URL key
}
```
`assignedIssues` is *not* a full `Issue` — it's a narrowed shape. The page computes it via `.sort((a,b) => b.updatedAt - a.updatedAt)` at line 695 but does not actually pick the narrowed fields — the narrowing is typed in the props interface only, which means the full `Issue` object is passed through despite the declaration. Redesign has access to every `Issue` field should it need one.

### State and hooks
None. Pure render function.

### Render structure
- Container (`div.space-y-8`)
  - `<LatestRunCard>` (see above)
  - Charts grid (`grid grid-cols-2 lg:grid-cols-4 gap-4`) — 4× `<ChartCard>` wrapping chart bodies
  - Recent Issues block (`div.space-y-3`)
    - Header row: h3 + right-aligned `Link` to `/issues?participantAgentId=…`
    - Either empty-state `<p>` or bordered container with up to 10 `<EntityRow>` children + optional "+N more" footer
  - Costs block (`div.space-y-3`)
    - h3
    - `<CostsSection>` (see below)

### Styling mechanism
Inline Tailwind, no `cva`, no `cn()`.

### External dependencies
- DS tokens: `border-border`, `text-muted-foreground`, `hover:text-foreground`.
- Raw-palette: none directly in `AgentOverview` itself — all raw-palette usage lives in children (`LatestRunCard`, the chart components, `runStatusIcons`).
- Shadcn primitives: none directly.
- Project components: `ChartCard`, `RunActivityChart`, `PriorityChart`, `IssueStatusChart`, `SuccessRateChart`, `StatusBadge`, `EntityRow`.
- Local helpers: `LatestRunCard`, `CostsSection`.

### Edge cases and conditionals
- `assignedIssues.length === 0` → renders "No recent issues." paragraph, no bordered container.
- More than 10 assigned issues → overflow footer `+N more issues`.
- Zero runs → Charts still render their own empty states ("No runs yet"). `LatestRunCard` disappears entirely. Costs section shows just the runtimeState KPI strip (if present), or renders nothing meaningful (no table, no KPIs).
- Agent is paused → no visual cue appears in `AgentOverview`. The page header shows the pause state via `StatusBadge`, and `PauseResumeButton` flips to "Resume," but nothing inside the dashboard area acknowledges paused state.
- Agent is `pending_approval` → header shows a yellow banner; dashboard content still renders normally.

---

## 3. `CostsSection` (`AgentDetail.tsx:1339–1413`)

### Props
```ts
{ runtimeState?: AgentRuntimeState; runs: HeartbeatRun[] }
```

### State and hooks
None. Derives `runsWithCost` inline (filter + sort, non-memoized — recomputes every render; fine given small `runs` count).

### Render structure
- Container (`div.space-y-4`)
  - Conditional KPI strip (`div.border.rounded-none.p-4` → `grid grid-cols-2 md:grid-cols-4 gap-4 tabular-nums`): Input / Output / Cached tokens + Total cost. Renders only when `runtimeState` exists.
  - Conditional per-run table (bordered wrapper → `<table>` with columns: Date / Run (8-char UUID prefix) / Input / Output / Cost). Top 10 rows by `createdAt` desc.

### Styling mechanism
Inline Tailwind, no `cva`.

### External dependencies
- DS tokens: `border-border`, `bg-accent/20`, `text-muted-foreground`.
- Raw-palette: none.
- Shadcn: none.
- Helpers: `formatTokens`, `formatCents`, `formatDate`, `runMetrics` (local).

### Edge cases and conditionals
- `!runtimeState` → KPI strip hidden.
- `runsWithCost.length === 0` → table hidden.
- If both are absent → the Costs block collapses to just its empty outer `<div>` (caller renders just the "Costs" h3 with nothing underneath). Worth noting: there is no "no cost data yet" empty state; it silently degrades to whitespace.

---

## 4. Data model surface (4 APIs)

### `agentsApi.runtimeState(agentId, companyId)` → `AgentRuntimeState`
Relevant fields:
- `sessionId`, `sessionDisplayId`, `stateJson` — adapter session state.
- `lastRunId`, `lastRunStatus` — pointer to most-recent run (lets a redesign show "health" without scanning the heartbeats array).
- `totalInputTokens`, `totalOutputTokens`, `totalCachedInputTokens` — aggregate counts for the lifetime of the current session.
- `totalCostCents` — lifetime cost for the current session.
- `lastError` — most-recent adapter-level error text.

**Important:** "total" here is session-lifetime, **not** "last 14 days," "this month," or "right now." If a redesign wants real-time burn rate, it has to derive that from per-run cost points.

### `heartbeatsApi.list(companyId, agentId?, limit?)` → `HeartbeatRun[]`
Per-run fields worth naming:
- `id`, `agentId`, `invocationSource` (`timer` | `assignment` | `on_demand` | `automation`), `triggerDetail`.
- `status` (`succeeded` | `failed` | `running` | `queued` | `timed_out` | `cancelled` | `scheduled_retry`).
- `startedAt`, `finishedAt`, `createdAt`, `updatedAt` (all `Date`).
- `usageJson`, `resultJson` — unstructured records. `runMetrics()` parses `{inputTokens|input_tokens}`, `{outputTokens|output_tokens}`, `{cachedInputTokens|...}`, and `visibleRunCostUsd(usage, result)`.
- `error`, `errorCode`, `stderrExcerpt` — failure context for debug mode.
- `livenessState`, `livenessReason`, `continuationAttempt`, `lastUsefulActionAt`, `nextAction` — liveness signals for a running run.

**No field links a run to an issue** directly in the narrow `HeartbeatRun` type. (`ActiveRunForIssue` and `LiveRunForIssue` are separate helper types used on the issue side.)

### `issuesApi.list(companyId, filters)` → `Issue[]`
Filter `{ participantAgentId }` is the filter used on this page. Returns full `Issue` records.

Relevant `Issue` fields for each mode:
- Monitoring: `status`, `priority`, `executionRunId`, `executionLockedAt` — "is the agent actively on this one."
- Operations: `priority` (4-bucket enum) is the *only* reorder-ish knob. No `order`, `rank`, `position`, or agent-personal ordering field.
- Accountability: no cost rollup per-issue exists in `Issue` itself.
- Debug: `executionRunId` lets you jump from a failed issue to the run that ran it.

Mutation surface: `issuesApi.update(id, data)` accepts arbitrary PATCH data. No reorder endpoint. `issuesApi.checkout(id, agentId)` assigns for execution.

### `budgetsApi.overview(companyId)` → `BudgetOverview`
- `policies: BudgetPolicySummary[]` — one per scope (`company`, `agent`, `project`). Each has `amount`, `observedAmount`, `remainingAmount`, `utilizationPercent`, `warnPercent`, `status: "ok"|"warning"|"hard_stop"`, `paused`, `pauseReason`, `windowStart`, `windowEnd`.
- `activeIncidents: BudgetIncident[]`, `pausedAgentCount`, `pausedProjectCount`, `pendingApprovalCount`.

`AgentDetail` already filters `policies` to find the one with `scopeType === "agent" && scopeId === agent.id` and synthesizes one from `agent.budgetMonthlyCents` / `spentMonthlyCents` as a fallback. The synthesized one is what downstream budget UI actually uses.

**Refetch cadence:** `budgetOverview` query has `refetchInterval: 30_000, staleTime: 5_000`. So it's genuinely live data — a redesign that uses it gets 30s-granularity updates for free.

---

## 5. Resolving the "dead code?" question on `budgetOverview`

**Not dead.** Used at `AgentDetail.tsx:699` to compute `agentBudgetSummary`, which is consumed by `BudgetPolicyCard` rendered inside the Budget tab (not the Dashboard tab). The Dashboard tab fetches it (via the `enabled: !!resolvedCompanyId` gate, not gated to dashboard-only) but doesn't use it in `AgentOverview`.

**Implications for redesign:**
- Threading `agentBudgetSummary` into `AgentOverview` props costs one line at the call site (1091–1099) and is consistent with the "all current data must remain reachable" principle.
- The summary exposes: `observedAmount`, `remainingAmount`, `utilizationPercent`, `status`, `paused`, `warnPercent`, `windowEnd`. All are directly useful for the primary-goal "live economics" rubric item.
- Using it doesn't require a new token or new component — `BudgetPolicyCard` already renders this shape.

Not a recommendation yet — just noting the option exists.

---

## 6. Action affordance reality check

### Pause agent — ✅ already implemented (open interpretation question)
- `agentsApi.pause(id, companyId)` and `agentsApi.resume(id, companyId)` exist (`agents.ts:147–148`).
- `PauseResumeButton` component exists (`AgentActionButtons.tsx:23`) and is already wired in the page header with the exact semantics the brief specifies: single-click, reversible via same button, no confirmation dialog, neutral outline styling, `<Pause>` / `<Play>` icons from lucide.
- `AgentStatus` enum includes `paused` (`constants.ts:16`), and `Agent.pauseReason` exists on the type.
- **Question:** does the brief want pause *also* surfaced inside `AgentOverview`, or does it consider the header instance as already satisfying the requirement? The rubric ("Pause agent. Single-click action, reversible via the same button…") doesn't specify placement.

### Reorder in-flight tasks — 🔴 blocked (needs brief clarification)
- No `order`, `rank`, `position`, `displayOrder`, or user-personal ordering field anywhere.
- `Issue.priority` is a closed 4-value enum. Even if we reinterpret drag-reorder as "promote/demote by priority bucket," that's 4 coarse steps, not arbitrary ordering.
- `AgentTaskSession` has no ordering field.
- No reorder endpoint on `issuesApi`, `agentsApi`, `heartbeatsApi`, or elsewhere.
- `@dnd-kit` is installed (`ui/package.json:32-34`) and used in `KanbanBoard.tsx` and `SidebarProjects.tsx`. **But:** `KanbanBoard.handleDragEnd` persists only *column* (status) changes on drop (line 247–249). Within-column ordering is lost on refresh. `SidebarProjects` also does not persist ordering.
- There is no pending-tasks queue surface in the app today. The current dashboard shows "Recent Issues" (history, not queue) and that's it.

**What's physically possible:**
- **(a) Local-only reorder** — UI works, but order doesn't survive refresh or cross-user. Fails the rubric's intent (a teammate can't set an order for another teammate to see).
- **(b) Priority-bucket promote/demote** — drag into one of 4 buckets. Feasible via `issuesApi.update(id, { priority })`. Coarse and doesn't really "reorder"; more like "re-classify."
- **(c) Flag as scope blocker** — revise the action to something achievable today, or revise the brief.

**My flag, not a recommendation:** "reorder in-flight tasks" as written does not correspond to a feature the data model supports. Before I design around it, I need your call on (a) / (b) / (c), or something else.

### Navigate to detail — ✅ already works
- `LatestRunCard` is a `<Link>` to `/agents/[agent]/runs/[runId]`.
- `EntityRow` in the Recent Issues list takes a `to` prop that currently points at `/issues/[id]`.
- The source-tag pill and StatusBadge do not independently navigate (parent `<Link>` owns click).

No API gap. The brief's caveat "preserve current link-based navigation unless a better affordance is clearly warranted" is trivially satisfied.

---

## 7. DS observations (things I didn't expect)

- **Cyan-pulse color drift across live-run indicators.** Inline `LatestRunCard` uses `bg-cyan-400`; the header's mobile-only live indicator (`AgentDetail.tsx:955–956`) uses `bg-blue-400` / `bg-blue-500`. Same "agent is live right now" concept, two different hues, no coordination. Not a blocker, but if I preserve the cyan-pulse on the redesigned dashboard, the header+dashboard combination will still be inconsistent. The fix is out of scope (header is outside the redesign region).
- **`ChartCard` placeholder uses `rounded-sm`.** In `ActivityCharts.tsx:113`, the empty-bar placeholder div has `rounded-sm`. The Step 0 radius migration swept `rounded-lg` / `rounded-xl` to `rounded-none` but did not touch `rounded-sm`. So it's consistent with Step 0 policy (pre-existing, not drift I'd be introducing), just worth noting before anyone asks "why is there a rounded corner on the charts." If the redesign avoids empty-state bars entirely, this goes away.
- **`bg-accent/20` in `CostsSection` table header.** This is the DS `accent` token (good), just with a custom opacity suffix. Not drift — flagging because the redesign may want to match or deliberately change it.
- **`runStatusIcons` uses raw-palette tones** (`text-green-600`, `text-red-600`, `text-cyan-600`, etc.). This is in scope per location (inside `AgentDetail.tsx:103–111`) but its deeper source is that `status-colors.ts` is the canonical catalog for entity status and is explicitly locked. If the redesign wants to change status colors, that's out of scope by policy. If it wants to *use* them consistently, it should route through `StatusBadge` / `StatusIcon` rather than the inline `runStatusIcons` map.

---

## 8. Things harder than the brief implies

1. **"Surface reorder on the dashboard" is meaningfully harder than "surface pause on the dashboard."** Pause is a one-line mutation with a ready-made button. Reorder is a data-model gap.
2. **"Live economics" as a primary-goal rubric item** has two reasonable interpretations that require different data sources:
   - *Session burn rate* — derive from `runtimeState.totalCostCents` / session duration. Session-lifetime only.
   - *Budget position* — use `agentBudgetSummary.observedAmount` / `remainingAmount`. Month-to-date.
   Both are useful; only the second is genuinely "live" via 30s refetch. The first is "live" only in that `runtimeState` refreshes. The brief's wording leaves both open — I'll need to pick.
3. **The dashboard currently doesn't acknowledge `paused` state at all.** If the primary-goal "activity state: running/idle/paused/error" item is a hard requirement, that's new content, not redesigned content. The existing `StatusBadge` in the header serves the need at page level, so the redesign has to decide whether to duplicate within the dashboard frame.
4. **"Fit without scrolling on 1440px"** is tight once the existing data is preserved. Current dashboard is ~1,600–1,800px tall based on the screenshots. Consolidation (chart merges, cost/token unification) is mandatory, not optional, to hit the goal.

---

## Summary of what I need from you at the checkpoint

Explicit answers on:

1. **Reorder scope.** Pick: (a) local-only UI reorder, (b) priority-bucket drag, (c) revise/drop the reorder action, or (d) something else. I can sketch what each looks like if helpful.
2. **Pause placement.** Duplicate-on-dashboard, move-into-dashboard, or header-instance-already-counts?
3. **"Live economics" interpretation.** Session burn rate, budget position, or both?
4. **Paused-state acknowledgement on the dashboard.** Required for Section 1 pass, or tolerated as header-only?
5. **Anything above that doesn't match what you intended** when you wrote the brief.

No design work yet — waiting for your read.
