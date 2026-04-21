# Quota Display

Visual representation of "used vs budget" for provider quotas, subscription quotas, or billing windows.

**Instances: 2 (below the 3+ threshold, documented per directive).**
`QuotaBar` (2 uses), `ProviderQuotaCard` (1 use).

> **Extraction-only pass.** Documents the pair; does not prescribe a merge. See [components-review.md §Likely duplicates #9](../components/components-review.md#9-quota-display-providerquotacard--quotabar).

## Instances

| Component | Lines | Uses | Role |
|---|---|---|---|
| `QuotaBar.tsx` | 65 | 2 | Single horizontal bar — % used with optional deficit notch |
| `ProviderQuotaCard.tsx` | 416 | 1 | Full per-provider card — composes `QuotaBar` + `ClaudeSubscriptionPanel`/`CodexSubscriptionPanel` |

## Composition

`QuotaBar`:

```ts
interface QuotaBarProps {
  label: string;
  percentUsed: number;              // 0–100
  leftLabel: string;
  rightLabel?: string;
  showDeficitNotch?: boolean;
  className?: string;
}
```

- Single horizontal bar with a filled portion.
- Fill color is computed from a threshold function:
  - `>90% → bg-red-400`
  - `>70% → bg-yellow-400`
  - `else → bg-green-400`

`ProviderQuotaCard`:

- Composes `QuotaBar` (multiple times for different windows: 5h / 24h / 7d rolling + period budget), plus `ClaudeSubscriptionPanel` / `CodexSubscriptionPanel` for vendor-native quota windows.
- Three-level wrapper around `QuotaBar`.

## Token drift inherent to this pattern

`QuotaBar` hardcodes `bg-red-400`, `bg-yellow-400`, `bg-green-400` — raw Tailwind palette. This is a **fourth** place where the app encodes three-level severity:

1. `status-colors.ts` — red-600/500/400, amber-400, yellow-400, etc.
2. `ActivityCharts.tsx` — direct hex values
3. `BudgetPolicyCard` / `BudgetIncidentCard` / `BudgetSidebarMarker` — 3-level severity with 500/90% alpha
4. `QuotaBar` — 3-level severity with solid 400s

All four encode "health / warn / hard-stop" but none share a token. See [tokens-review.md §1, §4](../tokens/tokens-review.md) for the token-drift picture and [patterns-review.md](./patterns-review.md) for the severity-indicator pattern opportunity.

## Different affordances, not a duplicate

The pair is **not** a duplicate in the literal sense — `QuotaBar` is a rendering primitive, `ProviderQuotaCard` is a composer. The naming suggests parallels (`-Bar` vs `-Card`) but their roles are distinct:

- Use `QuotaBar` for a single measured ratio.
- Use `ProviderQuotaCard` for a full provider's worth of quotas.

What the name spread **does** flag: there's no shared concept of "quota display primitive" — `QuotaBar` is one, but the finance/accounting cards use their own bar-less metric cells (`<Cell label value />` triples), and `BudgetPolicyCard` uses hand-rolled borders. The family would benefit from either promoting `QuotaBar` into a shared DS primitive for all "used vs budget" visualizations, or acknowledging that each surface wants a different look.

## Scale caveat

Only 2 instances — the pattern is thinly attested. Listed here per founder directive; a real "quota display" DS pattern would need a third independent caller before it's real.

## Related

- [finance-card.md](./finance-card.md) — `BillerSpendCard` also composes `QuotaBar`.
- [subscription-panel.md](./subscription-panel.md) — `ProviderQuotaCard` composes both subscription panels.
- [patterns-review.md §Severity indicator](./patterns-review.md) — the wider family of 3-level severity displays.
