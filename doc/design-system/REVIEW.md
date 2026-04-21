# Paperclip DS Extraction — Review

- **Generated:** 2026-04-21
- **Repo SHA:** `a26e1288b627e82c554445732c7d844648e6b5e1`
- **Branch:** `sockmonster-ds-extraction`
- **Discovery config:** [`_discovery.json`](./_discovery.json)
- **Scope:** `ui/` (`@paperclipai/ui`). Plugin SDK (`packages/plugins/sdk/src/ui/`) treated as contract surface, not implementation surface.

This is the entry point. Everything else is linked from here. Contents are ordered by **expected human value**, not by stage.

---

## Bottom line

One finding sits upstream of most of the others — resolving it moves four pattern docs from "pending" to "codifiable" and unblocks the single biggest token gap.

> **The app has a canonical status/priority color catalog (`ui/src/lib/status-colors.ts`) that bypasses the DS token layer and uses raw Tailwind palette classes across 11 hues and ~24 status keys.** Status indicators (`StatusIcon`, `StatusBadge`, `PriorityIcon`, `agentStatusDot`), chart colors (`ActivityCharts.tsx`, hardcoded hex), budget severity indicators (`BudgetPolicyCard`, `BudgetIncidentCard`, `BudgetSidebarMarker`), and quota fills (`QuotaBar`) are **four distinct systems encoding the same red/amber/green severity concept**, none of which share DS tokens.

A `--signal-*` token family would collapse four surfaces onto one vocabulary and make [status-display.md](./patterns/status-display.md), [quota-display.md](./patterns/quota-display.md), and the severity-indicator pattern opportunity all codifiable. See [tokens-review.md §4](./tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds) and [patterns-review.md §6](./patterns/patterns-review.md#6-severity-indicator-3-level-health-display--pattern-opportunity).

Three other findings are high-value but smaller in scope:
- **`destructive-foreground` has a buggy light-mode value** equal to `destructive` itself (would render invisible if anyone used it — nobody does, so the bug is masked). [tokens-review.md §2](./tokens/tokens-review.md#2-destructive-foreground-has-a-wrong-light-mode-value-and-is-unused)
- **13 color tokens are dead** (all 5 `chart-*`, all 8 `sidebar-*`). Consolidating would drop color-token count from 32 → 19. [tokens-review.md §1, §3](./tokens/tokens-review.md#1-chart--tokens-are-dead)
- **The radius scale is non-monotonic and under-specified** — 227 uses of `rounded-lg` / `rounded-xl` resolve to square corners because `--radius-lg` / `--radius-xl` = 0. Needs a founder call on whether this is intentional flat-design or a stale migration state. [tokens-review.md §Radius scale](./tokens/tokens-review.md#radius-scale--under-founder-review)

---

## Recommended review order

Sequenced so each step unblocks the next. Total time estimated ~2–3 hours.

| # | Read | Decide | Est. |
|---|---|---|---|
| 1 | [tokens-review.md §High-confidence drift](./tokens/tokens-review.md#high-confidence-drift-likely-should-be-fixed) | Scope the signal-token work. Confirm dead-token deletions (chart-*, sidebar-*, destructive-foreground). | **25 min** |
| 2 | [tokens-review.md §Radius scale](./tokens/tokens-review.md#radius-scale--under-founder-review) | One call: intentional flat lg/xl, or restore a monotonic scale. | **15 min** |
| 3 | [components-review.md §Likely duplicates](./components/components-review.md#likely-duplicates) | Nine duplicate families. For each, note "merge/keep/defer" — patterns flow from the decisions. | **30 min** |
| 4 | [components-review.md §Plugin SDK contract gap](./components/components-review.md#plugin-sdk-contract-gap) | Choose: fulfill the 9 missing contracts, shrink them, or hybrid. | **15 min** |
| 5 | [patterns-review.md §Variance across documented patterns](./patterns/patterns-review.md#variance-across-documented-patterns-whats-inconsistent-between-instances) | Look at the status-element variance in detail pages (four different treatments across eight pages). | **15 min** |
| 6 | [patterns-review.md §Paperclip-domain patterns](./patterns/patterns-review.md#paperclip-domain-patterns-worth-calling-out-opportunities-not-ratified-patterns) | Reality-check the run-transcript / heartbeat / metric-cell opportunities before any codify step. | **20 min** |
| 7 | [components-review.md §Naming inconsistencies](./components/components-review.md#naming-inconsistencies) | Lower priority — no decision required today, but at least skim. | **10 min** |
| 8 | [components-review.md §Story coverage gaps](./components/components-review.md#story-coverage-gaps) | Shadcn primitives missing from `foundations.stories.tsx` (`collapsible`, `dropdown-menu`, `avatar`, `skeleton`, `scroll-area`) is a small, targeted fix. | **10 min** |

---

## Confidence

### High confidence (probably correct, spot-check only)

- **32 color tokens** extracted from `ui/src/index.css` (19 semantic surfaces, 5 chart, 8 sidebar).
- **5 radius tokens**, with value + definition-site recorded.
- **Usage counts per color and radius token** computed by unioning Tailwind-utility occurrences and `var(--token)` references across `ui/src/**/*.{ts,tsx,css}` (excluding the definition file itself). Counts are rough by intent — within ±10%.
- **135 component files** enumerated, classified into 22 primitives / 64 composites / 47 standalones / 2 non-component utilities.
- **104 components** cross-referenced against 14 Storybook files via import-graph parsing.
- **50 pages** enumerated; per-page import set captured.
- **11 plugin SDK ambient components** enumerated with host-implementation status.
- **4 components confirmed as storybook-only** (0 production uses): `AccountingModelCard`, `AgentProperties`, `CompanySwitcher`, `ExecutionParticipantPicker`.

### Medium confidence (review carefully)

- **Duplicate-family flags.** Eight families surfaced ([components-review.md §Likely duplicates](./components/components-review.md#likely-duplicates)) are based on name parallelism and/or shared imports. The strongest signals (entity-creation dialogs, subscription panels) need a side-by-side diff to confirm merge-ability; this extraction didn't do that.
- **`BillerSpendCard` vs `FinanceBillerCard` as likely-true-duplicate.** Flagged per directive. Not confirmed without a diff.
- **Pattern instance counts.** The `detail-page` and `list-page` patterns were identified by import-set intersection, which is a proxy for structural similarity. A page can import a component and not actually render it in the expected position; pattern shape is inferred, not verified pixel-by-pixel.
- **CVA variant extraction for primitives.** Parsed 3 files successfully (`button`, `badge`, one more). The rest of the primitives likely have variants that the static parser missed.
- **The severity-indicator pattern ([patterns-review.md §6](./patterns/patterns-review.md#6-severity-indicator-3-level-health-display--pattern-opportunity)).** Named as an *opportunity*, not a ratified pattern — cross-system evidence is strong but the four systems weren't compared pixel-for-pixel; they may not actually agree on what "warning" looks like.
- **Story coverage set.** Computed by parsing imports in `.stories.tsx` files. A component that's imported by a story but never actually rendered would falsely appear covered. Low risk given story-file structure but not validated.

### Low confidence (likely wrong, incomplete, or judgment-heavy)

- **Motion tokens.** None exist as variables — motion is inline `@keyframes` + `cubic-bezier()`. Pattern docs don't describe motion. The 5 keyframes in `index.css` are listed; their callers are not cross-referenced.
- **Typography.** No project-local font/type tokens found — the section is near-empty because Tailwind v4 defaults carry the load plus `@tailwindcss/typography`. If there are intended type-scale conventions in components that weren't captured by token extraction, those are missed.
- **Elevation / shadows.** No tokens, so no inventory. Ad-hoc `shadow-[…]` values across polished surfaces were enumerated in [tokens-review.md §9](./tokens/tokens-review.md#9-arbitrary-shadow-values-in-production-surfaces), but the list is not exhaustive.
- **Prop extraction for primitives using `React.ComponentProps<"button"> & VariantProps<...>`.** The static parser looks for `*Props` interfaces; inline-type components (most shadcn primitives) get "no Props interface found" in their detail files.
- **Per-component token consumption cross-reference.** Components/detail files don't list which specific tokens each component consumes (would require per-file class-attribute parsing). Token usage counts are global; per-component token drift is flagged only where specific drift was found.
- **Pattern: "detail-page header."** Called out as a sub-pattern inside detail-page doc but not given its own file — instances share only 4–5 imports, not a complete shape.

---

## Known scope limitations

- **Plugin SDK UI.** In-scope as a contract surface (documented in [components/index.md §Plugin SDK contracts](./components/index.md#plugin-sdk-contracts-11)). Not in-scope for pattern extraction — host implementations are covered; plugin-side usage patterns are not.
- **Low-usage components (1–2 code imports, 76 of them).** Listed in [components/index.md](./components/index.md) with status marker `📘 below-threshold`; no dedicated detail file. Per the directive: *nothing gets silently dropped*.
- **Pattern documentation:** capped at 10 real patterns. Eleven pattern files exist because the duplicate-family directive required documenting three below-threshold pairs (subscription-panel, sidebar-menu pair inside sidebar-chrome, quota-display). Pattern opportunities surfaced in patterns-review.md are not yet pattern files.
- **UX Lab pages (`InviteUxLab`, `IssueChatUxLab`, `RunTranscriptUxLab`).** Acknowledged prototypes with distinct visual language. Excluded from pattern extraction. Their raw-palette usage is counted in drift stats but not pursued.
- **Hermes / adapter code.** `ui/src/adapters/` contains per-adapter config fields. Not a DS concern; skipped.
- **Mobile treatments.** `MobileBottomNav` and `SwipeToArchive` are noted but not extracted as their own pattern. Mobile patterns appear to live inside individual list/detail pages rather than as shared primitives.
- **Diff mode.** This is a fresh run; `doc/design-system/` did not exist before. No diff was generated. Subsequent re-runs should run in diff mode (see [ds-extraction skill §Diff mode](../../.agents/skills/ds-extraction/SKILL.md#diff-mode)).

---

## What's on disk

```
doc/design-system/
├── REVIEW.md                                   ← you are here
├── _discovery.json                             ← Stage 0 output
├── _pages.json                                 ← Stage 2 scratch (50 pages)
├── _composition-graph.json                     ← Stage 2 scratch (135 components)
├── _stories.json                               ← Stage 2 scratch (14 stories)
├── tokens/
│   ├── tokens.md                               ← canonical human-readable inventory
│   ├── tokens.json                             ← machine-readable for downstream tooling
│   └── tokens-review.md                        ← the high-value drift artifact
├── components/
│   ├── index.md                                ← all 135 files + 11 SDK contracts, with status markers
│   ├── components-review.md                    ← duplicates, naming, token non-compliance, story gaps, SDK gap
│   └── [ComponentName].md × 53                 ← per-component detail files (3+ uses threshold)
└── patterns/
    ├── index.md
    ├── patterns-review.md                      ← variance, opportunities, what to resolve before re-running
    ├── list-page.md                            ← 12 instances
    ├── detail-page.md                          ← 8 instances
    ├── sidebar-chrome.md                       ← 6 + 2 instances
    ├── finance-card.md                         ← 5 instances
    ├── entity-properties-panel.md              ← 4 + 1 instances (open Q on generic)
    ├── entity-creation-dialog.md               ← 4 instances
    ├── status-display.md                       ← 3 components + catalog (pending signal tokens)
    ├── entity-row.md                           ← 3 instances
    ├── subscription-panel.md                   ← 2 instances (below threshold — documented)
    └── quota-display.md                        ← 2 instances (below threshold — documented)
```

Total: **~80 files**.
