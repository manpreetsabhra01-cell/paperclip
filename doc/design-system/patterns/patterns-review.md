# Patterns Review

Cross-cutting findings from Stage 4. Ordered by expected human value.

- **Generated:** 2026-04-21
- **Inventory:** [index.md](./index.md)
- **Upstream dependencies:** [../tokens/tokens-review.md](../tokens/tokens-review.md), [../components/components-review.md](../components/components-review.md)

---

## Variance across documented patterns (what's inconsistent between instances)

### Status-element variance in detail pages ([detail-page.md](./detail-page.md))

Four different ways detail pages render the entity's status:

| Detail page | Status treatment |
|---|---|
| `IssueDetail` | `StatusIcon` + `StatusBadge` |
| `AgentDetail` | `StatusBadge` + `agentStatusDot` (a helper in `status-colors.ts`, not a component) |
| `ProjectDetail`, `GoalDetail` | `StatusBadge` alone |
| `ApprovalDetail` | Tone encoding inside `ApprovalCard`, no badge |

That's four variations across eight pages. Tied to the broader [status-display.md](./status-display.md) and [tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds) issues.

### Loading-state variance across list and detail pages

`PageSkeleton` is used in 22 places, but 3 of 8 detail pages and 3 of 12 list pages skip it. Each of those pages has its own ad-hoc loading handling (or doesn't render until data arrives). Worth auditing for consistency.

### Row variance across list pages ([list-page.md](./list-page.md) + [entity-row.md](./entity-row.md))

`EntityRow` (generic, 6 uses) is never used on any main list page. Every list page rolls its own row: `IssueRow` for Inbox/Swipe, grid cards for Agents, Kanban columns for Issues, `GoalTree` for Goals, etc. Either the list pages should adopt `EntityRow` or `EntityRow` has a gap that keeps it from fitting (status/unread/mobile-responsive).

---

## Paperclip-domain patterns worth calling out (opportunities, not ratified patterns)

These are the checklist patterns from the extraction skill — run-transcript row, heartbeat indicator, agent card, cost display, approval gate, metric cell. Each is examined for whether it currently exists as a 3+ pattern, a thinner-than-threshold pattern, or a clear opportunity.

### 1. Run transcript row

**Status:** a single parent component (`RunTranscriptView.tsx`, 3 uses) owns the rendering. Not a cross-cutting pattern at the instance level.

- `RunTranscriptView` lives in `ui/src/components/transcript/`.
- Used by `AgentDetail`, `IssueDetail`, `RunTranscriptUxLab`.
- Has its own test (`RunTranscriptView.test.tsx`) and a sibling hook (`useLiveRunTranscripts.ts`).
- No Storybook story — see [components-review.md §Story coverage gaps](../components/components-review.md#story-coverage-gaps).

**Opportunity:** if transcript rows (agent action + timestamp + tool call summary) were exposed as a `<TranscriptRow>` primitive, downstream surfaces (embedded transcripts, search results, activity feed) could reuse it. Currently the rendering is internal to `RunTranscriptView`.

### 2. Heartbeat / liveness indicator

**Status:** one component (`LiveRunWidget`, 1 use) plus scattered inline usage in `AgentDetail` (imports `heartbeatsApi`). Not a pattern.

- `LiveRunWidget.tsx` is a "live" indicator with cyan border glow; 90 lines; only used on Dashboard.
- `AgentDetail` renders its own live-status dot (`border-cyan-500/30 shadow-[0_0_12px_rgba(6,182,212,0.08)]` hardcoded).
- `ActiveAgentsPanel` also renders live markers.

**Opportunity:** extract a `<Heartbeat live={bool} lastSeenAt={Date}>` primitive. Today the three surfaces each reinvent the visual treatment.

### 3. Agent card

**Status:** fragmented across `ActiveAgentsPanel` (1 use, composite), `Agents` page (rolls its own), and `SidebarAgents` (1 use). No shared `AgentCard`.

**Opportunity:** unify into a single card pattern if Stage 3's "Card vs Panel vs Widget" naming question gets resolved.

### 4. Approval gate UI

**Status:** `ApprovalCard` (3 uses), `ApprovalPayload` (4 uses), `OutputFeedbackButtons` (2 uses). Three components, all in the approval flow, but each a single surface — not repeated across three independent contexts.

- `ApprovalCard` and `ApprovalPayload` appear in `ApprovalDetail` page and in `Approvals` list. `OutputFeedbackButtons` appears in run detail surfaces.

**Status call:** not enough cross-cutting instance count to formalize as a pattern today. The trio is **already** the approval-gate pattern — just not repeated enough to name.

### 5. Cost / budget display

**Status:** six related components (`BudgetPolicyCard`, `BudgetIncidentCard`, `BudgetSidebarMarker`, `QuotaBar`, `ProviderQuotaCard`, `BillerSpendCard`) span two adjacent pattern docs ([finance-card.md](./finance-card.md), [quota-display.md](./quota-display.md)).

**Not merged here** — the finance / accounting slice documents the "reporting card" shape; the quota slice documents the "used vs budget" shape; the budget slice hand-rolls its own 3-level severity indicator (see §6). These three related but distinct domains would benefit from a shared glossary before pattern consolidation.

### 6. Severity indicator (3-level health display) — **pattern opportunity**

[Not documented as a dedicated pattern file in this pass; surfaced here as the most impactful opportunity.]

The app has **four distinct systems** for encoding "healthy / warning / critical":

| System | Location | Example |
|---|---|---|
| `status-colors.ts` | `issueStatusText.todo = "text-blue-600 dark:text-blue-400"` etc. | 11 hues, -600/-400 weights |
| `ActivityCharts.tsx` hardcoded hex | `critical: "#ef4444"` | Raw hex, no dark-mode variant |
| `BudgetPolicyCard` / `BudgetIncidentCard` | `"text-red-300 border-red-500/30 bg-red-500/10"` | 3-level with 300/500/10% alpha |
| `BudgetSidebarMarker` | `"bg-emerald-500/90 text-white"` | 3-level with 500/90% alpha |
| `QuotaBar` | `"bg-red-400"` etc. | 3-level solid 400 |

Each encodes the same *concept* (red/amber/green severity), each chooses different *Tailwind utility classes*. None of them reference the DS tokens — because there are no DS tokens for this. See [tokens-review.md §4 — status-colors.ts is a canonical semantic-color catalog that bypasses the DS](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds).

**Prerequisite:** a `--signal-*` token family (`--signal-success`, `--signal-warning`, `--signal-error`, each with `-bg`/`-text`/`-border`/`-subtle` variants). Once tokens exist, a `<SeverityIndicator level="ok|warn|critical">` primitive becomes writable and all four sites collapse onto it.

**Why this is the highest-leverage pattern opportunity:** it sits downstream of Stage 1's biggest finding (§4) and unblocks at least three patterns at once (status-display, quota-display, cost-display). Do not try to codify those three separately first.

### 7. Metric cell (small uppercase label + tabular-num value)

Recurring micro-pattern spotted in `FinanceBillerCard`:

```tsx
<div className="border border-border p-3">
  <div className="text-[11px] uppercase tracking-[0.14em] text-muted-foreground">debits</div>
  <div className="mt-1 font-medium tabular-nums">{formatCents(...)}</div>
</div>
```

Appears inline in `FinanceBillerCard`, `BillerSpendCard`, `ProviderQuotaCard`, and (with small variations) in some detail-page headers and dashboard surfaces. Used ad hoc rather than as a reusable `<MetricCell>` primitive. Pattern exists informally; could be extracted to ~15 lines.

### 8. Metadata grid (label-value pairs, common in detail views)

Per the skill checklist. Not surfaced as a recurring shape in the current codebase's detail pages — each detail page renders its metadata inline. `KeyValueList` is contracted in the plugin SDK but not implemented. Weak pattern; opportunity.

---

## Candidates for future documentation (below threshold today)

Patterns glimpsed at 1–2 instances. If usage grows, they'd warrant dedicated docs:

- **Toolbar / filter bar** — `FilterBar` (1), `IssueFiltersPopover` (2), inline filter popovers on several list pages. No shared shape yet.
- **Inline editor** — `InlineEditor` (6 uses) plus the `DraftInput` / `DraftTextarea` primitives from `agent-config-primitives`. The inline-edit interaction has a shape; not yet abstracted as a named pattern.
- **Modal image / file viewer** — `ImageGalleryModal`, `DocumentDiffModal` (and `PathInstructionsModal`). Three modals with different content, possibly sharing chrome.
- **Kanban column** — `KanbanBoard` (2 uses) + `IssueColumns` (4 uses) + `IssueGroupHeader` (1). Issue-specific; pattern lives inside the issue domain.

---

## What to resolve before Stage 4 would run cleanly on re-extraction

Pattern extraction is limited by upstream ambiguity. The following decisions, if made, would let the next pattern re-run produce materially tighter docs:

1. **Signal token family** ([tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds)). Blocks [status-display.md](./status-display.md), part of [quota-display.md](./quota-display.md), and §6 above.
2. **Radius scale decision** ([tokens-review.md §Radius scale](../tokens/tokens-review.md#radius-scale--under-founder-review)). Once `rounded-lg` / `rounded-xl` behave predictably, the UxLab pages can be brought into the main DS conversation instead of ignored.
3. **Plugin SDK contract disposition** ([components-review.md §Plugin SDK contract gap](../components/components-review.md#plugin-sdk-contract-gap)). Dictates whether `KeyValueList` / `LogView` / `ActionBar` / `Spinner` / `DataTable` / `TimeseriesChart` become host patterns or stay aspirational.
4. **Naming vocabulary** ([components-review.md §Naming inconsistencies](../components/components-review.md#naming-inconsistencies)). Not strictly required for patterns to exist, but the docs here have to use neutral language (_"dialog pattern — some implementations named *Modal, same primitive"_) because the vocabulary is unresolved. A canonical decision would tighten pattern prose.
