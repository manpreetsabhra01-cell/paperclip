# Components Review

- **Generated:** 2026-04-21
- **Repo SHA:** a26e1288b627e82c554445732c7d844648e6b5e1
- **Inventory:** [index.md](./index.md)
- **Token drift feeding components:** [../tokens/tokens-review.md](../tokens/tokens-review.md)
- **Pattern docs drawn from this review:** [../patterns/index.md](../patterns/index.md)

---

## How to read this document

This is a **persistent backlog**, not just a snapshot review. It captures opportunities identified during DS extraction. **All items are intentionally deferred** per the 2026-04-21 decision: no component merges, no renames, no file-casing changes were made. Address opportunistically when the relevant code is touched for other reasons, or schedule as standalone follow-up projects.

Each entry below includes:
- A **problem statement** — what was observed and why it might matter.
- **Affected files** — so the next developer doesn't have to re-trace.
- A **suggested resolution** — or "needs discussion" when the right call is not obvious.

Entries are ordered by expected value, not by stage. Sections are self-contained — you can pick one up cold without reading the rest.

---

## Likely duplicates

Pairs or families whose names, compositions, or role overlap enough that they may be consolidatable. None of these are automatic merges — each is a judgment call. Items 1–7 and 9 are documented as single patterns in [../patterns/](../patterns/) so the family is visible to future pattern extraction; this section captures the consolidation opportunity.

### 1. Entity-creation dialog family: `NewAgentDialog` / `NewGoalDialog` / `NewIssueDialog` / `NewProjectDialog`

Four parallel "create new X" dialogs, each used 1–2 times, each a composite that wraps a form-in-a-dialog. Strong candidate for a single generic `NewEntityDialog` or a `useNewEntityForm` hook plus entity-specific field sets.

| File | Uses |
|---|---|
| `ui/src/components/NewAgentDialog.tsx` | 1 |
| `ui/src/components/NewGoalDialog.tsx` | 1 |
| `ui/src/components/NewIssueDialog.tsx` | 2 |
| `ui/src/components/NewProjectDialog.tsx` | 1 |

**Verify:** open the four files and diff them. If ≥60% body overlap, consolidate. Impact on Stage 4: otherwise a "New-entity dialog" pattern would be proposed with 4 instances, which is better represented as a single component.

### 2. Properties-panel family: `AgentProperties` / `GoalProperties` / `IssueProperties` / `ProjectProperties` (+ generic `PropertiesPanel`)

**Problem.** Four entity-specific property-panel components sit next to a generic `PropertiesPanel`. It is unclear whether `PropertiesPanel` **composes** the four (i.e., hosts their body via a context slot — which is what the 29-line `PropertiesPanel.tsx` appears to do) or whether the four **duplicate** the outer-chrome work that `PropertiesPanel` could own. `AgentProperties` is also unused in production (Storybook-only).

**Affected files.**

| File | Uses | Notes |
|---|---|---|
| `ui/src/components/PropertiesPanel.tsx` | 1 | 29-line generic chrome; reads `panelContent` from `usePanel()` context |
| `ui/src/components/IssueProperties.tsx` | 2 | 1370-line entity-specific body |
| `ui/src/components/GoalProperties.tsx` | 1 | Entity-specific body |
| `ui/src/components/ProjectProperties.tsx` | 1 | 1140-line entity-specific body |
| `ui/src/components/AgentProperties.tsx` | **0** | Storybook-only — see §Unused components |

**Suggested resolution: needs discussion.** Per the 2026-04-21 decision, the open question of "composes vs duplicates" is **flagged but not resolved**. Reading the source suggests `PropertiesPanel` is the outer slot and the four bodies are what gets passed into it — so not a literal duplicate. But the four bodies each re-roll section headers, separators, save-state plumbing, and field layout; those might be factor-able into a shared `<PropertiesPanelBody>` helper. Open `IssueProperties.tsx` and `ProjectProperties.tsx` side-by-side before making any call. Documented as-is in [../patterns/entity-properties-panel.md](../patterns/entity-properties-panel.md).

### 3. Subscription panel pair: `ClaudeSubscriptionPanel` ↔ `CodexSubscriptionPanel`

**Problem.** Two components with parallel names, parallel props shape (both accept `windows: QuotaWindow[]` + optional `source` / `error`), rendering ordered subscription-quota windows for a single provider. Both used exactly once — always dispatched from `ProviderQuotaCard`. When a third vendor (Gemini? Cursor?) is added, the pattern becomes "a third copy-paste" unless consolidated. Today's cost of keeping them separate is minor because the pair is only 2.

**Affected files.**

- `ui/src/components/ClaudeSubscriptionPanel.tsx` (1 use — 140 lines)
- `ui/src/components/CodexSubscriptionPanel.tsx` (1 use)
- `ui/src/components/ProviderQuotaCard.tsx` — composes both, dispatches by vendor

**Suggested resolution.** Diff-test the two files. If ≥70% body overlap, collapse to `SubscriptionPanel({ vendor: "claude" | "codex" | … })` with per-vendor window-key config as data. If divergence is higher, keep the pair and let `ProviderQuotaCard` continue to dispatch. Documented as-is in [../patterns/subscription-panel.md](../patterns/subscription-panel.md).

### 4. Sidebar-menu pair: `SidebarAccountMenu` ↔ `SidebarCompanyMenu`

**Problem.** Two dropdown components anchored to the sidebar slot — one for account actions (user profile, sign out), one for company actions (switch company, settings). Same visual affordance (dropdown triggered from a sidebar button), different data subject. Each used twice; neither is a hotspot.

**Affected files.**

- `ui/src/components/SidebarAccountMenu.tsx` (2 uses)
- `ui/src/components/SidebarCompanyMenu.tsx` (2 uses)

**Suggested resolution.** Read both and check the body overlap. Natural consolidations if similar: `<SidebarMenu kind="account" | "company">` driven by kind, or a more general `<SidebarMenu>` that accepts items via a `children` slot. If the bodies are genuinely different (different menu items, different trigger layout), the pair stays and the shared pattern is the sidebar-slot anchoring — which is already captured by `SidebarNavItem`. Documented as-is in [../patterns/sidebar-chrome.md — §The sidebar-menu pair](../patterns/sidebar-chrome.md#the-sidebar-menu-pair).

### 5. Finance card family: `BillerSpendCard` / `FinanceBillerCard` / `FinanceKindCard` / `FinanceTimelineCard` / `AccountingModelCard`

**Problem.** Five cards in the finance/accounting surface, each used 0–1 times, all sharing the shadcn `Card` family as substrate. Two specific flags surfaced per the 2026-04-21 directive:

1. **`BillerSpendCard` ↔ `FinanceBillerCard` — likely a true duplicate.** Both name "Biller" in their filename. Both render a per-biller financial summary. They consume **different data models** (`CostByBiller` vs `FinanceByBiller`) — either two genuinely different reporting concepts whose names fail to distinguish them, or one superseded the other and the predecessor survived. Line counts differ (145 vs 44), consistent with a "rich" / "slim" pair.
2. **`AccountingModelCard` is unused.** Zero imports anywhere in the codebase; only appears in Storybook. Either abandoned or awaiting a page.

**Affected files.**

| File | Uses | Data model | Lines |
|---|---|---|---|
| `ui/src/components/BillerSpendCard.tsx` | 1 | `CostByBiller` + `CostByProviderModel` | 145 |
| `ui/src/components/FinanceBillerCard.tsx` | 1 | `FinanceByBiller` | 44 |
| `ui/src/components/FinanceKindCard.tsx` | 1 | `FinanceByKind` (inferred) | — |
| `ui/src/components/FinanceTimelineCard.tsx` | 1 | timeline rollup | — |
| `ui/src/components/AccountingModelCard.tsx` | **0** | — | — |

**Suggested resolution.**
- Diff `BillerSpendCard` vs `FinanceBillerCard` side-by-side. Rename for clarity, merge, or confirm as distinct-but-adjacent.
- Decide `AccountingModelCard`'s fate — adopt it into a page, or delete.
- The broader family (4 cards) is naturally a "Finance / accounting card" pattern. Documented as-is in [../patterns/finance-card.md](../patterns/finance-card.md).

### 6. Row family: `ActivityRow` / `EntityRow` / `IssueRow`

**Problem.** Three row components, one generic and two entity-specific. `EntityRow` is the generic (6 uses), `IssueRow` is issue-specific with an extensive slot interface (3 uses — Inbox + SwipeToArchive), `ActivityRow` is activity-event-specific (2 uses — Activity page). `IssueRow`'s 15-field prop shape (six of them optional `ReactNode` slot props) looks like it could be expressed as `<EntityRow kind="issue" />` plus issue-specific defaults.

**Affected files.**

| File | Uses | Notes |
|---|---|---|
| `ui/src/components/EntityRow.tsx` | 6 | Truly generic — leading / identifier / title / subtitle / trailing slot API |
| `ui/src/components/IssueRow.tsx` | 3 | Composes `StatusIcon`; unread-state + archive action + mobile/desktop split |
| `ui/src/components/ActivityRow.tsx` | 2 | Composes `Identity`, `IssueReferenceActivitySummary` — activity-event-specific |

**Suggested resolution.** Compare `IssueRow` against `EntityRow` directly. If the six slot props plus `unreadState` / `onArchive` can be implemented as `EntityRow` extensions (default slots keyed on `issue`), collapse. `ActivityRow`'s activity-verb formatting is genuinely specialized and is likely worth keeping separate even if `IssueRow` consolidates. Also relevant: the main list pages (Issues, Agents, Projects, …) don't use `EntityRow` today — see [../patterns/list-page.md — Open questions](../patterns/list-page.md#open-questions--risks). Documented as-is in [../patterns/entity-row.md](../patterns/entity-row.md).

### 7. Sidebar triad: `Sidebar` / `InstanceSidebar` / `CompanySettingsSidebar` + `CompanyRail` + `CompanySettingsNav` + `MobileBottomNav`

**Problem.** Six components with overlapping responsibilities — all are "chrome around the main view." Not literal duplicates because each targets a different surface (main nav vs instance settings vs company settings vs company switcher vs tab nav vs mobile bottom bar), but the three-word vocabulary (Sidebar / Rail / Nav) obscures whether these are variants of one structural pattern or separate components that happen to live near each other. They share navigation primitives (`SidebarNavItem`, `SidebarSection`) but not a unifying wrapper. The dead `sidebar-*` tokens in `ui/src/index.css` were designed for this family and none of these consume them (see [tokens-review.md §3](../tokens/tokens-review.md#3-sidebar--tokens-are-dead)).

**Affected files.**

| File | Uses | Role (inferred) |
|---|---|---|
| `ui/src/components/Sidebar.tsx` | ≥3 | Main app navigation |
| `ui/src/components/InstanceSidebar.tsx` | 1 | Instance-settings scope |
| `ui/src/components/CompanySettingsSidebar.tsx` | 2 | Company-settings scope |
| `ui/src/components/CompanyRail.tsx` | 1 (260 lines, dnd-kit-driven) | Sortable company switcher rail |
| `ui/src/components/access/CompanySettingsNav.tsx` | 1 | Settings-page top tab nav |
| `ui/src/components/MobileBottomNav.tsx` | ≥3 | Mobile-bottom-tab alternative |

**Suggested resolution: needs discussion.** Three possible framings:
- **(a) They are genuinely different components** (different layouts, different primitives, different affordances) and the naming convergence is coincidental. Closest to how the code reads today.
- **(b) They are variants of a `<Sidebar variant="main" | "settings" | "rail" | …>`** and should consolidate under one name. Requires auditing their visual shape.
- **(c) They are three distinct patterns** — "sidebar" (persistent rail), "rail" (narrow-strip), "nav" (tab-bar) — and the current spread is correct but the naming convention for picking between them isn't written down anywhere.

Tied to [§Naming inconsistencies — Sidebar / Rail / Nav](#sidebar--rail--nav). Documented as-is in [../patterns/sidebar-chrome.md](../patterns/sidebar-chrome.md).

### 8. Status display triad: `StatusIcon` / `StatusBadge` / `PriorityIcon`

**Problem.** Three components render entity status/priority across the app with the same visual language but different affordances. All three consume `ui/src/lib/status-colors.ts` — a canonical TypeScript catalog mapping status strings to raw Tailwind-palette classes. Two of the three (`StatusIcon`, `PriorityIcon`) type their primary prop as an **untyped string**; `StatusBadge` uses a typed variant. This inconsistency plus the fact that the catalog bypasses the DS token layer makes this a pattern-shape-pending item rather than a straightforward duplicate flag.

**Affected files.**

| File | Uses | Prop type | Notes |
|---|---|---|---|
| `ui/src/components/StatusIcon.tsx` | 14 | `status: string` (untyped) | Circle + popover picker |
| `ui/src/components/StatusBadge.tsx` | 19 | `{ status: string }` wrapping `statusBadge[status]` | 15-line pill |
| `ui/src/components/PriorityIcon.tsx` | 5 | `priority: string` (untyped) | Arrow/triangle + popover picker |
| `ui/src/lib/status-colors.ts` | — | — | Catalog consumed by all three |
| `ui/src/components/AgentActionButtons.tsx` | — | — | Consumes `agentStatusDot` from the same catalog |

**2026-04-21 status.** The token-side of this problem was **partially addressed**: `--signal-success` / `--signal-success-foreground` landed as action-severity tokens paired with `--destructive` (see [tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds)). `status-colors.ts` itself is **not** touched — tokenizing its entity-state coloring into a `--status-*` family is a deferred future project. So the two problems here are now independent:
- **Signal (action severity)** = tokens exist, no consumers yet, opt-in.
- **Status (entity state)** = catalog stays as raw Tailwind palette, unchanged.

**Suggested resolution: do not codify a unified status-display pattern in this DS pass.** Pattern shape is explicitly pending the eventual signal-token / status-token scoping. When that project happens, the shape of these three components (typed enum props, class naming, default-fallback behavior) will want to change in sync. Documented as-is in [../patterns/status-display.md](../patterns/status-display.md).

Separately — and independent of the tokens — the `status: string` / `priority: string` untyped props could be typed as string literal unions over the keys of `status-colors.ts`'s records without touching colors. That's a small, self-contained follow-up.

### 9. Quota display: `ProviderQuotaCard` ↔ `QuotaBar`

**Problem.** Two components share the "quota" root name but differ in suffix and in role. `QuotaBar` is a rendering primitive — one horizontal bar with a percent-used fill and a three-level color threshold. `ProviderQuotaCard` is a card composer that *uses* `QuotaBar` (multiple times, for different time windows) plus `ClaudeSubscriptionPanel` / `CodexSubscriptionPanel`. Functionally different, but the `-Bar` / `-Card` naming spread suggests parallelism that isn't there.

Secondary concern: `QuotaBar` hardcodes three-level severity as raw Tailwind palette (`bg-red-400`, `bg-yellow-400`, `bg-green-400`). It is one of four places in the codebase that encode the same red/amber/green severity language without shared tokens — see [../tokens/tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds) and [../patterns/patterns-review.md §6 — Severity indicator](../patterns/patterns-review.md#6-severity-indicator-3-level-health-display--pattern-opportunity).

**Affected files.**

- `ui/src/components/QuotaBar.tsx` (2 uses — 65 lines — primitive bar)
- `ui/src/components/ProviderQuotaCard.tsx` (1 use — 416 lines — card composer that embeds `QuotaBar`)

**Suggested resolution.** Not a consolidation target — different roles. The naming can be clearer if a future refactor happens (e.g., rename the composer to `ProviderQuotaSummary` or `QuotaOverviewCard`). Separate concern: the three-level severity coloring in `QuotaBar` would collapse onto signal/status tokens when that broader work lands. Documented as-is in [../patterns/quota-display.md](../patterns/quota-display.md).

---

## Naming inconsistencies

**Status: deferred (2026-04-21).** All vocabulary decisions below (Dialog-vs-Modal, Picker-vs-Selector, Editor-vs-Form, Sidebar-vs-Rail-vs-Nav, Card-vs-Panel-vs-Widget, file-name casing, prop-vocabulary conventions) are intentionally unresolved. Addressing opportunistically when the relevant code is touched for other reasons, or as a standalone naming-pass project. Each subsection is self-contained — pick any one up cold without reading the others. In pattern docs under `../patterns/`, vocabulary is noted as observed variance (e.g., "dialog pattern — some implementations named `*Modal`, same primitive") rather than as a canonical prescription.

### Container-word proliferation: `Card` vs `Panel` vs `Widget` vs `Modal` vs `Dialog`

Counts of components by suffix:

| Suffix | Count | Examples |
|---|---|---|
| `Card` | 12 | `ApprovalCard`, `BudgetPolicyCard`, `MetricCard`, … |
| `Panel` | 5 | `ActiveAgentsPanel`, `ClaudeSubscriptionPanel`, `PropertiesPanel`, … |
| `Widget` | 1 | `LiveRunWidget` |
| `Modal` | 3 | `DocumentDiffModal`, `ImageGalleryModal`, `PathInstructionsModal` |
| `Dialog` | 6 | `NewAgentDialog`, `ExecutionWorkspaceCloseDialog`, `RoutineRunVariablesDialog`, … |

**Dialog vs Modal:** both Modal- and Dialog-named components use the same `dialog.tsx` primitive. There's no structural distinction — just two names for the same thing. Pick one. The shadcn default is `Dialog`; keeping `Dialog` is the lower-friction move.

**Card vs Panel:** less clear-cut. Rough pattern in this codebase:
- `*Card` when the thing is a discrete piece of content in a grid (`BudgetPolicyCard`, `FinanceBillerCard`).
- `*Panel` when the thing is a larger region that groups related content (`ActiveAgentsPanel`, `PropertiesPanel`).
- But there are violations: `ClaudeSubscriptionPanel` and `AccountingModelCard` look alike structurally and are adjacent in usage.

**Widget (1):** only `LiveRunWidget` uses this. Either absorb into `Card`/`Panel` or codify `Widget` as a distinct concept (e.g., "dashboard-tile with its own data fetch and refresh cadence") and use it consistently.

### `Picker` vs `Selector`

| Name | Uses | Picks what |
|---|---|---|
| `AgentIconPicker` | 13 | An icon |
| `ExecutionParticipantPicker` | 0 (unused) | A participant |
| `ReportsToPicker` | 2 | An agent |
| `InlineEntitySelector` | 1 | An entity |

All four wrap a popover-plus-list. Picker and Selector are synonymous here. Pick one term.

### `Editor` vs `Form`

| Name | Uses | Purpose |
|---|---|---|
| `MarkdownEditor` | 16 | Markdown input |
| `InlineEditor` | 6 | Generic text editor |
| `EnvVarEditor` | 2 | Structured key=value list |
| `RoutineVariablesEditor` | 2 | Structured variable list |
| `ScheduleEditor` | 1 | Cron schedule |
| `AgentConfigForm` | 5 | Full agent config |
| `JsonSchemaForm` | 1 | Schema-driven form |

The line between Editor and Form is fuzzy: `RoutineVariablesEditor` looks like a form, `JsonSchemaForm` could have been named `JsonSchemaEditor`. Suggested rule: **Editor** for content inputs (text, schedule, markdown); **Form** for labeled-field structured forms. Audit whether any renaming is worth the churn; otherwise document the rule and enforce for new additions.

### Sidebar / Rail / Nav

For what are structurally all "chrome around the main content area":

- `Sidebar.tsx`
- `InstanceSidebar.tsx`
- `CompanySettingsSidebar.tsx`
- `CompanyRail.tsx`
- `MobileBottomNav.tsx`
- `access/CompanySettingsNav.tsx`

Pick a default term (`Sidebar`) and use a prefix for variants (`InstanceSidebar`, `CompanySettingsSidebar`). Reserve `Rail` for genuinely different (narrow-strip) affordances, `Nav` for tab-bar-style navigation. The current split is inconsistent with itself.

### File-naming convention is inconsistent

Most composites are `PascalCase.tsx`. Shadcn primitives are `kebab-case.tsx`. But `agent-config-primitives.tsx` and `agent-config-defaults.ts` sit in the top-level composite directory in kebab-case — they don't belong with the shadcn primitives (not in `ui/`) but also don't match the PascalCase of their neighbors.

Suggested fix: rename `agent-config-primitives.tsx` → `AgentConfigPrimitives.tsx` (or split into per-component files if the 11 exports warrant it) and `agent-config-defaults.ts` → `agentConfigDefaults.ts` to match JS convention for non-component modules.

### Prop vocabulary is underspecified

A static scan for conventional variant-shaping props (`variant`, `size`, `intent`, `tone`, `kind`, `state`, `status`, `mode`, `level`, `severity`, `priority`) found them used in just **7** components across the codebase (excluding shadcn primitives where they're well-defined):

| Prop | Component | Type |
|---|---|---|
| `variant` | `IssueChatThread` | `"full" \| "embedded"` |
| `variant` | `PageSkeleton` | `... \| "list"` (opaque) |
| `size` | `Identity` | `IdentitySize` |
| `kind` | `ProjectWorkspaceSummaryCard` | `"project_workspace" \| "execution_workspace"` |
| `status` | `StatusIcon` | `string` (**untyped**) |
| `mode` | `RunTranscriptView` | `TranscriptMode` |
| `priority` | `PriorityIcon` | `string` (**untyped**) |

Observations:
- `status: string` in `StatusIcon` and `priority: string` in `PriorityIcon` should be typed enums (matching the keys of `status-colors.ts`).
- `variant` is used for completely unrelated concepts in different components — that's fine semantically, but reinforces that there's no shared prop-vocabulary convention.
- Shadcn primitives (`button`, `badge`) use `variant`/`size` with well-defined CVA enums — these are the model.

---

## Token non-compliance

(Mirror of the Stage 1 token drift findings, but attributed per-component so Stage 3 reviewers can target the worst offenders.)

### Components that hardcode chart/status colors

From [tokens-review.md §1 and §4](../tokens/tokens-review.md):

| Component | Drift |
|---|---|
| `ActivityCharts.tsx` | 17 hardcoded Tailwind-palette hex values for status/priority chart colors; uses `chart-*` tokens zero times. |
| `OrgChart.tsx` (a page) | 6 hardcoded hex values for agent status dot colors. |
| `StatusIcon.tsx` (14 uses) | Consumes `issueStatusIcon` from `status-colors.ts` — raw Tailwind palette classes (`text-blue-600`, `border-violet-600`, etc.). |
| `StatusBadge.tsx` (19 uses) | Consumes `statusBadge` from `status-colors.ts` — raw Tailwind palette classes. |
| `PriorityIcon.tsx` (5 uses) | Consumes `priorityColor` — raw Tailwind palette. |
| `AgentActionButtons.tsx` | Uses `agentStatusDot` — raw Tailwind palette. |

### Components with heavy raw-palette styling

From Stage 1's 659-hit analysis:

- `AgentDetail.tsx` (75 palette hits) — production page
- `RunTranscriptView.tsx` (47 hits) — production component
- `IssueChatThread.tsx` (22 hits) — production component

### Components with arbitrary radius values

See [tokens-review.md §7](../tokens/tokens-review.md#7-arbitrary-radius-values-bypass-the-scale-18-occurrences) for the full list. Production components in the list:
- `CompanyRail.tsx` — `rounded-[14px]`, `rounded-[22px]`
- Several UxLab pages (acceptable as prototypes)

### Recommendation

Do not extract per-component detail docs for `StatusIcon`, `StatusBadge`, `PriorityIcon`, `AgentActionButtons` as final specs — their color language is blocked on the signal-token decision from [tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds). Stage-4 patterns that lean on these (status indicator, priority indicator, agent card with status) will change shape once signal tokens land.

---

## Story coverage gaps

Components with **3+ code uses and no Storybook coverage** — the highest-priority story gaps:

| Component | Uses | Notes |
|---|---|---|
| `StatusIcon` | 14 | Central status-color consumer. Coverage gap tied to §Token non-compliance. |
| `collapsible` (primitive) | 8 | Shadcn primitive. `foundations.stories.tsx` covers many primitives but skips collapsible. |
| `dropdown-menu` (primitive) | 8 | Same. |
| `avatar` (primitive) | 7 | Same — despite Avatar being one of the few components with sub-parts (`AvatarGroup`, `AvatarFallback`, `AvatarBadge`). |
| `skeleton` (primitive) | 6 | Same. |
| `scroll-area` (primitive) | 3 | Same. |
| `ApprovalPayload` | 4 | Feature. |
| `IssueReferencePill` | 4 | Feature. |
| `SidebarNavItem` | 3 | Structural. |
| `RunTranscriptView` | 3 | Feature — the transcript rendering. |

Two categories:
1. **Shadcn primitives missing from `foundations.stories.tsx`.** Small, targeted fix — add them to the existing foundations story.
2. **Production features without a story.** `StatusIcon` is the highest value given its role across 14 call sites.

---

## Unused / low-signal components

### Truly dead (0 imports, no Storybook coverage): 0

None. Every file in `ui/src/components/` either gets imported somewhere or appears in a story.

### Storybook-only (0 imports, appears in a story): 4

These are rendered in a story file but never imported by any page or other component:

| Component | Storybook location (approx.) |
|---|---|
| `AccountingModelCard` | financial/accounting-related story |
| `AgentProperties` | agent-management story |
| `CompanySwitcher` | navigation-layout story |
| `ExecutionParticipantPicker` | (story-referenced; unused in app) |

**Interpretation:** these are either (a) abandoned experiments still living in Storybook, (b) components waiting for the page that uses them, or (c) genuinely unused and should be deleted. Recommend: owner disposition per file. `AgentProperties` is especially surprising given the existence of sibling `IssueProperties`/`GoalProperties`/`ProjectProperties` — possibly a planned-but-not-wired variant of the properties family.

### Below-threshold (1–2 code uses, no detail file): 76

Not drift — many are legitimately single-use (one-off dialogs, one-off banners). But at 76 out of ~130 it's worth noting: this codebase heavily favors single-use components. A generic-component consolidation pass would likely shrink this group by ~30%. The candidates from §Likely duplicates above are the best places to start.

---

## Plugin SDK hybrid status, prioritization deferred

**Status: RESOLVED as intentional hybrid (2026-04-21).** Not drift. The plugin SDK (`packages/plugins/sdk/src/ui/components.ts`) declares 11 ambient component types; the host implements 2 (`MetricCard`, `StatusBadge`) and leaves the other 9 as contract-only. The 9 unimplemented components now carry a `@status contract-only` JSDoc tag in the SDK source so plugin authors see the status in IDE tooltips at call sites.

Prioritization of which of the 9 to build first is a **separate plugin-SDK roadmap conversation** — not a DS decision and not in scope here. This section captures the current state and the most likely first-implementations when that conversation happens.

### Current state

| SDK contract | Host implementation | Status |
|---|---|---|
| `MetricCard` | [`ui/src/components/MetricCard.tsx`](../../../ui/src/components/MetricCard.tsx) | ✅ implemented |
| `StatusBadge` | [`ui/src/components/StatusBadge.tsx`](../../../ui/src/components/StatusBadge.tsx) | ✅ implemented |
| `DataTable` | — | 🔌 contract-only |
| `TimeseriesChart` | — | 🔌 contract-only |
| `MarkdownBlock` | — | 🔌 contract-only |
| `KeyValueList` | — | 🔌 contract-only |
| `ActionBar` | — | 🔌 contract-only |
| `LogView` | — | 🔌 contract-only |
| `JsonTree` | — | 🔌 contract-only |
| `Spinner` | — | 🔌 contract-only |
| `ErrorBoundary` | — | 🔌 contract-only |

Each of the 9 contract-only entries has a JSDoc block in [`packages/plugins/sdk/src/ui/components.ts`](../../../packages/plugins/sdk/src/ui/components.ts) (lines 253–316) that tells plugin authors the runtime will fail and points here.

`PLUGIN_SPEC.md:30` already acknowledged this before the DS extraction: _"The current runtime does not yet ship a real host-provided plugin UI component kit."_

### Implementation notes (for when prioritization happens)

Not prescriptive — candidates surfaced during the 2026-04 extraction:

- **`MarkdownBlock`** — thinnest wrapper around `ui/src/components/MarkdownBody.tsx`. Possibly an alias rather than a new component.
- **`Spinner`** — no matching host component. A ~10-line shadcn-style primitive would be the simplest new build.
- **`KeyValueList`** — patterns exist ad-hoc inside `EntityRow`, `PropertiesPanel` bodies, and `FinanceBillerCard`. Candidate for extraction into a shared primitive.
- **`LogView`** — no counterpart. Transcript rendering is tightly coupled to `RunTranscriptView`; a generic log viewer is a genuine new build.
- **`JsonTree`** — no counterpart. A new build.
- **`ErrorBoundary`** — standard React pattern; a thin wrapper around a React error boundary class.
- **`ActionBar`**, **`DataTable`**, **`TimeseriesChart`** — each is a real component's worth of surface area. Not thin builds.

### Affected files if the prioritization conversation opens

- `packages/plugins/sdk/src/ui/components.ts` (SDK declarations + @status tags)
- `packages/plugins/sdk/src/ui/runtime.ts` (runtime bridge — `renderSdkUiComponent`)
- New host files in `ui/src/components/` for each implemented contract
- [`components/index.md` — Plugin SDK contracts table](./index.md#plugin-sdk-contracts-11) (update implementation column as each lands)
