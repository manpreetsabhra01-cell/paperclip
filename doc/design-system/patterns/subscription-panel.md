# Subscription Panel

Panel summarizing a per-vendor subscription quota (rolling windows, reset times, session vs weekly).

**Instances: 2 (below the 3+ threshold, documented per directive).**
`ClaudeSubscriptionPanel`, `CodexSubscriptionPanel`.

> **Extraction-only pass.** Documents the pair as it exists; does not prescribe a collapse to a single `SubscriptionPanel({ vendor })`. See [components-review.md §Likely duplicates #3](../components/components-review.md#3-subscription-panel-pair-claudesubscriptionpanel--codexsubscriptionpanel).

## Instances

| Component | Lines | Uses | Vendor |
|---|---|---|---|
| `ClaudeSubscriptionPanel.tsx` | 140 | 1 | Anthropic Claude |
| `CodexSubscriptionPanel.tsx` | (unread) | 1 | OpenAI Codex |

## Composition

`ClaudeSubscriptionPanel` signature:

```ts
interface ClaudeSubscriptionPanelProps {
  windows: QuotaWindow[];
  source?: string | null;
  error?: string | null;
}
```

- Takes an array of `QuotaWindow` (from `@paperclipai/shared`).
- Renders ordered windows (session, week-all-models, week-sonnet-only, week-opus-only, extra-usage).
- Shows a reset timestamp per window using `toLocaleString`.

`CodexSubscriptionPanel` is parallel — same shape of inputs, same conceptual layout, different window ordering and different label normalization presumably.

Both are composed by `ProviderQuotaCard` — the host that decides "this is a Claude provider, render `ClaudeSubscriptionPanel`; this is a Codex provider, render `CodexSubscriptionPanel`."

## Variance

- **Window keys differ** (`currentsession`, `currentweekallmodels`, `currentweeksonnetonly`, … in Claude; likely a different set in Codex).
- **Label rules differ** — each has its own `normalizeLabel`.
- **Reset-time rendering is identical pattern** (`window.resetsAt → toLocaleString`).
- **Error surface is the same prop**.

## Scale caveat

Only 2 instances. The "pattern" shape is strong because the components look parallel, but there's no third point to triangulate from. If a third vendor is added (Gemini? Cursor?), the pattern becomes real; until then, two parallel files that share ~60% structure.

## Open questions

- Should `ProviderQuotaCard` own the vendor-specific rendering directly, or should the pair stay as separate files the card dispatches to?
- Are the two files diffable down to a `windowsConfig` data shape + a shared renderer? This would be the path if consolidation is pursued.

## Related

- [finance-card.md](./finance-card.md) — `BillerSpendCard` and `ProviderQuotaCard` are the orchestrator cards that consume these subscription panels.
- [tokens-review.md §4](../tokens/tokens-review.md) — subscription panels may consume status-color logic for "over-quota" state; confirm during any future consolidation.
