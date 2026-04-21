# Status Display

How the app renders entity status (for issues, runs, agents, goals, approvals, projects) and priority (for issues).

**Instances: 3 components + 1 module.**
`StatusIcon` (14 uses), `StatusBadge` (19 uses), `PriorityIcon` (5 uses), and `status-colors.ts` (the canonical catalog that all three consume).

> **Pattern shape pending signal-token scoping** — see [tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds). Any pattern codification that fixes the current prop shape or color-binding will likely change once the signal-token family lands.

## Instances

### `StatusIcon` (14 uses, `ui/src/components/StatusIcon.tsx`)

Small circular status indicator for issue status.

```ts
interface StatusIconProps {
  status: string;            // untyped — see Open questions
  onChange?: (status: string) => void;
  className?: string;
  showLabel?: boolean;
}
```

- Renders a `<span>` shaped as a circle with `border-2` and a hue from `issueStatusIcon[status]`.
- Includes a special "done" variant that fills the circle.
- If `onChange` is provided, wraps in a `<Popover>` with a picker of all statuses.
- Used across issue lists, properties panels, inbox, issue detail.

### `StatusBadge` (19 uses, `ui/src/components/StatusBadge.tsx`)

Pill-shaped status badge for any entity type.

```ts
interface Props {
  status: string;            // untyped — see Open questions
}
```

- 15-line component — wraps a `<span>` with `statusBadge[status]` classes.
- Uses `rounded-full px-2.5 py-0.5 text-xs` — a fixed shape across all statuses.
- `status` prop accepts an open string. The `statusBadge` record in `status-colors.ts` has 24 known keys covering agent / goal / run / approval / issue domains.

### `PriorityIcon` (5 uses, `ui/src/components/PriorityIcon.tsx`)

Small priority indicator (up/down/flat arrow or warning triangle).

```ts
interface PriorityIconProps {
  priority: string;          // untyped — see Open questions
  onChange?: (priority: string) => void;
  className?: string;
  showLabel?: boolean;
}
```

- Renders one of four lucide icons (`ArrowUp`, `ArrowDown`, `Minus`, `AlertTriangle`) per priority level.
- Same popover-when-onChange pattern as `StatusIcon`.
- Priority values: `critical`, `high`, `medium`, `low`.

### `status-colors.ts` (`ui/src/lib/status-colors.ts`)

Canonical status-and-priority color catalog:

```ts
export const issueStatusIcon: Record<string, string>  = { … };
export const issueStatusText: Record<string, string>  = { … };
export const statusBadge: Record<string, string>      = { … };
export const agentStatusDot: Record<string, string>   = { … };
export const priorityColor: Record<string, string>    = { … };
```

The file's header says _"Every component that renders a status indicator should import from here so colors stay consistent."_ — the three components above do. So does `AgentActionButtons`, `ProjectProperties`, `AgentDetail`, and a handful of pages.

## Composition (shared pattern)

```
<statusValue-prop>  → <record lookup in status-colors.ts>  → <raw Tailwind-palette classes>  → <render>
```

All three components follow this template. The pattern is correct in spirit — there is a central catalog. But the catalog contains raw Tailwind palette values (`text-blue-600 dark:text-blue-400`, `bg-green-100 text-green-700`, etc.), not DS tokens.

## Untokenized colors

Per [tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds), this entire pattern bypasses the DS token layer. The 11 hues currently in play (`blue`, `cyan`, `sky`, `green`, `emerald`, `amber`, `orange`, `yellow`, `violet`, `red`, `neutral`) across 24+ status keys could consolidate into a `--signal-*` token family:

- `--signal-success`, `--signal-warning`, `--signal-error`, `--signal-info`, `--signal-in-progress`, `--signal-in-review`, `--signal-neutral` — each with `-bg`, `-text`, `-border` variants.

Not proposed as a concrete change here. Flagged as the token gap that blocks this pattern from being codifiable.

## Props are untyped strings

Both `StatusIcon.status: string` and `PriorityIcon.priority: string` are open strings — TypeScript doesn't prevent callers from passing `"banana"`. The records in `status-colors.ts` fall through to a `*Default` class when a key is missing, so bad input degrades silently.

Once signal tokens exist, these props should be typed enums whose members match the signal-token keys. Any callers passing arbitrary strings will then light up at compile time.

## Related patterns and token drift

- Page-level: [detail-page.md](./detail-page.md) (status appears in detail-page headers)
- The "severity indicator" family — `BudgetPolicyCard`, `BudgetIncidentCard`, `BudgetSidebarMarker`, `QuotaBar` — uses a **third** color system (hand-picked red/amber/emerald at varying opacities), distinct from `status-colors.ts` and from the dead `--chart-*` tokens. See [patterns-review.md §Candidates](./patterns-review.md) for notes.
- `AgentActionButtons` and `ActivityCharts` also consume `status-colors.ts` / hardcoded hex — see [components-review.md §Token non-compliance](../components/components-review.md#token-non-compliance).

## Do-not-codify (yet)

Do not propose concrete refactors of `StatusIcon` / `StatusBadge` / `PriorityIcon` shape before the signal-token work. The prop API (typed enums), class naming, and default-fallback behavior all depend on what signal tokens look like.
