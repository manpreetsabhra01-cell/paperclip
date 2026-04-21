# Finance / Accounting Card

Card surface for summarizing a financial or accounting slice: per-biller spend, per-kind spend, timeline totals, accounting-model totals.

**Instances: 5.** `BillerSpendCard`, `FinanceBillerCard`, `FinanceKindCard`, `FinanceTimelineCard`, `AccountingModelCard`.

> **Extraction-only pass.** Documents the family as it exists. Two specific items are flagged for the founder below; neither is auto-resolved.

## Instances

| Component | Lines | Uses | Data type (inferred) |
|---|---|---|---|
| `BillerSpendCard` | 145 | 1 | `CostByBiller` (+ `CostByProviderModel` breakdown) |
| `FinanceBillerCard` | 44 | 1 | `FinanceByBiller` |
| `FinanceKindCard` | (unread) | 1 | `FinanceByKind` (inferred) |
| `FinanceTimelineCard` | (unread) | 1 | timeline roll-up |
| `AccountingModelCard` | (unread) | **0** | (unknown — unused) |

## Composition (shared)

All five are composites that import the shadcn `Card` family (`Card`, `CardHeader`, `CardTitle`, `CardDescription`, `CardContent`, `CardFooter`) and render a titled card with a body.

`BillerSpendCard` additionally composes `QuotaBar`. That's the richer card in the family — quota visualization + provider breakdown + billing-type breakdown.

`FinanceBillerCard` is a plain summary card with a three-cell metric grid (`debits` / `credits` / `estimated`).

## Flag 1 — Likely true duplicate: `BillerSpendCard` ↔ `FinanceBillerCard`

Per the founder's directive in Stage 3, this pair is flagged for diff review.

- Both have "Biller" in the name.
- Both summarize per-biller financials.
- They consume **different** data models (`CostByBiller` vs `FinanceByBiller`), which suggests either (a) two different reporting concepts the names fail to distinguish, or (b) one of them is a stale parallel implementation of the other.
- Line counts differ significantly (145 vs 44), but that could mean `BillerSpendCard` is the richer one *and* `FinanceBillerCard` is the slimmed-down version of the same concept.

**Action suggested (not taken here):** open both side by side and judge whether they represent two legitimately different reports, or whether one superseded the other and the older survived.

## Flag 2 — `AccountingModelCard` is unused

Zero imports across the codebase. Storybook-only coverage. See [components-review.md §Unused](../components/components-review.md#unused--low-signal-components). Delete or adopt.

## Composition template (common shape)

[INFER] From `FinanceBillerCard` (the cleanest example):

```
<Card>
  <CardHeader>
    <div flex between>
      <div>
        <CardTitle>{providerDisplayName(row.biller)}</CardTitle>
        <CardDescription>{eventCount}, {kindCount} kinds</CardDescription>
      </div>
      <div text-right>
        <div text-lg tabular-nums>{formatCents(row.netCents)}</div>
        <div uppercase tracking-wide muted>net</div>
      </div>
    </div>
  </CardHeader>
  <CardContent>
    <grid 3-column>
      <Cell label="debits" value={formatCents(...)} />
      <Cell label="credits" value={formatCents(...)} />
      <Cell label="estimated" value={formatCents(...)} />
    </grid>
  </CardContent>
</Card>
```

The recurring sub-element — a small metric cell with uppercase-tracked muted label + tabular-num value — is a micro-pattern worth noting. It appears here and (less formally) on list pages. Candidate for a `<MetricCell>` helper.

## Variance

- `BillerSpendCard` composes `QuotaBar`; the others don't.
- Different data models (`CostByBiller` vs `FinanceByBiller` vs `FinanceByKind`) — confirm whether the shared shape is intentional convergence or a sign that they should share a common `FinanceCardRow` interface.
- Per-card formatting helpers (`formatCents`, `formatTokens`, `providerDisplayName`) live in `@/lib/utils` — shared. Good.
