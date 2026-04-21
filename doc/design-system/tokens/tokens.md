# Paperclip Design System — Tokens

- **Generated:** 2026-04-21
- **Repo SHA:** a26e1288b627e82c554445732c7d844648e6b5e1
- **Scope:** `ui/` (`@paperclipai/ui`)
- **Authoritative source:** [`ui/src/index.css`](../../../ui/src/index.css)
- **Review doc (drift & open questions):** [tokens-review.md](./tokens-review.md)

## Source & conventions

- **Tailwind v4** (CSS-first config). No `tailwind.config.*` file.
- **shadcn/ui** (`new-york` style, `neutral` base, `cssVariables: true`, `iconLibrary: lucide`, non-RSC).
- **Color space:** `oklch()` throughout.
- **Theme scoping:** `:root` for light mode; `.dark` for dark overrides. Dark mode is opt-in via the `dark` class through the custom variant `@custom-variant dark (&:is(.dark *))`.
- **Tailwind alias layer:** `@theme inline { --color-*: var(--<token>); }` exposes every semantic token as a Tailwind utility (`bg-background`, `text-foreground`, …). That's the mechanism — the `--<token>` at `:root` is authoritative.

## Token counts

| Category   | Count | Notes |
|------------|-------|-------|
| Color      | 34    | 19 semantic surfaces + 2 signal + 5 chart + 8 sidebar |
| Radius     | 5     | Scale under review — non-monotonic values. See [tokens-review.md](./tokens-review.md#radius-scale--under-founder-review). |
| Spacing    | 0     | Tailwind v4 defaults only. |
| Type       | 0     | Tailwind v4 defaults + `@tailwindcss/typography`. Markdown styling is hand-rolled via `.paperclip-markdown` / `.paperclip-mdxeditor-content` classes in `index.css`. |
| Motion     | 0     | No `--motion-*` / `--duration-*` variables. 5 named `@keyframes`; easing and duration expressed inline at use sites. |
| Elevation  | 0     | No `--shadow-*` tokens. Project uses borders + background shifts for elevation; occasional arbitrary `shadow-[…]` in UxLab / polished surfaces. |

## Color (34)

Each token has a light value (`:root`) and a dark value (`.dark`). Tailwind alias is `bg-<name>`, `text-<name>`, etc.

> **Separation of concerns.** `destructive` / `destructive-foreground` and `signal-success` / `signal-success-foreground` are **action-severity** tokens — used on buttons, toasts, and solid accent fills where the intent is "this click is destructive/successful." They are paired in `{base, -foreground}` form for solid-bg + contrasting-text use. They are **not** status-indicator colors for entity state (issue status, agent status, priority). Entity-state coloring lives in [`ui/src/lib/status-colors.ts`](../../../ui/src/lib/status-colors.ts) as a TypeScript catalog using raw Tailwind palette classes — deliberately separate from DS tokens. Tokenizing `status-colors.ts` as a `--status-*` family is a deferred future project; see [tokens-review.md §4](./tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds).

### Semantic surfaces (19)

| Token | Light | Dark | Uses |
|---|---|---|---|
| `background` | `oklch(1 0 0)` | `oklch(0.145 0 0)` | 183 |
| `foreground` | `oklch(0.145 0 0)` | `oklch(0.985 0 0)` | 372 |
| `card` | `oklch(1 0 0)` | `oklch(0.205 0 0)` | 52 |
| `card-foreground` | `oklch(0.145 0 0)` | `oklch(0.985 0 0)` | 1 ⚠ |
| `popover` | `oklch(1 0 0)` | `oklch(0.205 0 0)` | 11 |
| `popover-foreground` | `oklch(0.145 0 0)` | `oklch(0.985 0 0)` | 5 |
| `primary` | `oklch(0.205 0 0)` | `oklch(0.985 0 0)` | 35 |
| `primary-foreground` | `oklch(0.985 0 0)` | `oklch(0.205 0 0)` | 10 |
| `secondary` | `oklch(0.97 0 0)` | `oklch(0.269 0 0)` | 3 ⚠ |
| `secondary-foreground` | `oklch(0.205 0 0)` | `oklch(0.985 0 0)` | 2 ⚠ |
| `muted` | `oklch(0.97 0 0)` | `oklch(0.269 0 0)` | 90 |
| `muted-foreground` | `oklch(0.556 0 0)` | `oklch(0.708 0 0)` | 1540 |
| `accent` | `oklch(0.97 0 0)` | `oklch(0.269 0 0)` | 340 |
| `accent-foreground` | `oklch(0.205 0 0)` | `oklch(0.985 0 0)` | 14 |
| `destructive` | `oklch(0.577 0.245 27.325)` | `oklch(0.637 0.237 25.331)` | 160 |
| `destructive-foreground` | `oklch(0.985 0 0)` | `oklch(0.985 0 0)` | 0 |
| `border` | `oklch(0.922 0 0)` | `oklch(0.269 0 0)` | 701 |
| `input` | `oklch(0.922 0 0)` | `oklch(0.269 0 0)` | 8 |
| `ring` | `oklch(0.708 0 0)` | `oklch(0.439 0 0)` | 26 |

⚠ Flagged for review. See [tokens-review.md](./tokens-review.md).

### Signal (2)

Action-severity tokens paired with `destructive`. Intended for solid-accent fills on buttons, toasts, and confirmation surfaces where the action is semantically "success" (approve, confirm, ship).

| Token | Light | Dark | Uses |
|---|---|---|---|
| `signal-success` | `oklch(0.527 0.154 150.069)` | `oklch(0.627 0.194 149.214)` | 0 (new) |
| `signal-success-foreground` | `oklch(0.985 0 0)` | `oklch(0.985 0 0)` | 0 (new) |

Sourced from the canonical approve-action button treatment (`bg-green-700 hover:bg-green-600 text-white` across `ApprovalCard`, `ApprovalDetail`, `Inbox`). Tailwind aliases are `bg-signal-success`, `text-signal-success-foreground`, etc. No call sites migrated yet — tokens land as primitives for opt-in adoption.

`--signal-warning` and `--signal-info` are intentionally **not** defined — defer until a real use case appears. See [tokens-review.md §Deferred variants](./tokens-review.md#deferred-signal-variants).

### Chart (5) — Reserved

> **Status: Reserved.** These five tokens are preserved in `ui/src/index.css` for the future chart-tokenization project. **Do not consume them today.** Current chart implementations (`ActivityCharts.tsx`, `OrgChart.tsx`) use hardcoded Tailwind-palette hex values directly; those call sites will migrate onto the chart tokens (or onto a `--status-*` family) in a separate future project. Keeping the tokens here is a deliberate placeholder. See [tokens-review.md §Chart tokens — reserved](./tokens-review.md#1-chart--tokens-are-dead).

| Token | Light | Dark | Status |
|---|---|---|---|
| `chart-1` | `oklch(0.646 0.222 41.116)` | `oklch(0.488 0.243 264.376)` | Reserved |
| `chart-2` | `oklch(0.6 0.118 184.704)` | `oklch(0.696 0.17 162.48)` | Reserved |
| `chart-3` | `oklch(0.398 0.07 227.392)` | `oklch(0.769 0.188 70.08)` | Reserved |
| `chart-4` | `oklch(0.828 0.189 84.429)` | `oklch(0.627 0.265 303.9)` | Reserved |
| `chart-5` | `oklch(0.769 0.188 70.08)` | `oklch(0.645 0.246 16.439)` | Reserved |

### Sidebar (8) — Reserved

> **Status: Reserved.** Preserved for shadcn `Sidebar` primitive compatibility in case that primitive is reintroduced. The current custom `Sidebar.tsx` consumes the semantic surface tokens (`background`, `foreground`, `accent`, `border`) directly. **Do not consume these tokens today** unless a sidebar variant that needs its own theming is explicitly being built. See [tokens-review.md §Sidebar tokens — reserved](./tokens-review.md#3-sidebar--tokens-are-dead).

| Token | Light | Dark | Status |
|---|---|---|---|
| `sidebar` | `oklch(0.985 0 0)` | `oklch(0.145 0 0)` | Reserved |
| `sidebar-foreground` | `oklch(0.145 0 0)` | `oklch(0.985 0 0)` | Reserved |
| `sidebar-primary` | `oklch(0.205 0 0)` | `oklch(0.488 0.243 264.376)` | Reserved |
| `sidebar-primary-foreground` | `oklch(0.985 0 0)` | `oklch(0.985 0 0)` | Reserved |
| `sidebar-accent` | `oklch(0.97 0 0)` | `oklch(0.269 0 0)` | Reserved |
| `sidebar-accent-foreground` | `oklch(0.205 0 0)` | `oklch(0.985 0 0)` | Reserved |
| `sidebar-border` | `oklch(0.922 0 0)` | `oklch(0.269 0 0)` | Reserved |
| `sidebar-ring` | `oklch(0.708 0 0)` | `oklch(0.439 0 0)` | Reserved |

## Radius (5)

> **Scale resolved 2026-04-21.** Existing dashboard surfaces use `rounded-none` by intent (sharp, Swiss aesthetic) — 226 call sites in `ui/src/` migrated off `rounded-lg` / `rounded-xl`. The `lg` and `xl` values are restored to a monotonic scale for newer surfaces (dialogs, chat bubbles, card explorations) and for the one shadcn primitive that consumes them (`dialog.tsx`, which now renders with 10px-rounded corners).

| Token | Value | Scope | Maps to Tailwind | Uses (post-migration) |
|---|---|---|---|---|
| `--radius` | `0` | `:root` | `rounded` (no suffix, via default) | 127 |
| `--radius-sm` | `0.375rem` (6px) | `@theme inline` | `rounded-sm` | 49 |
| `--radius-md` | `0.5rem` (8px) | `@theme inline` | `rounded-md` | 310 |
| `--radius-lg` | `0.625rem` (10px) | `@theme inline` | `rounded-lg` | 1 (dialog.tsx primitive) |
| `--radius-xl` | `0.75rem` (12px) | `@theme inline` | `rounded-xl` | 0 (opt-in for new surfaces) |

`--radius` (`:root`, = 0) is the legacy shadcn base; it's consumed by the MDXEditor bridge (`--baseRadius`) and by a few `calc(var(--radius) ± Npx)` expressions inside `index.css`. The `@theme` scale is what Tailwind utilities resolve to.

### 18 arbitrary `rounded-[Npx]` exceptions — all intentional, preserved

None of the 18 values below match the new scale (6 / 8 / 10 / 12 px). Kept as-is per design intent. See [tokens-review.md §Radius workaround audit](./tokens-review.md#radius-workaround-audit-18-occurrences-all-retained) for the per-file disposition.

## Spacing (0)

No project-local spacing tokens. Uses the Tailwind v4 default scale (`p-1`, `gap-4`, etc., driven by the Tailwind-shipped `--spacing` base of 0.25rem).

## Type (0)

No project-local font-family, font-size, or line-height tokens. Typography sources:

- Tailwind v4 defaults (the `--text-*` family ships with `tailwindcss`).
- `@tailwindcss/typography` plugin (prose).
- Hand-authored rules on `.paperclip-markdown` and `.paperclip-mdxeditor-content` in `ui/src/index.css` (hardcoded `font-size`, `line-height`, `margin`). These are per-surface overrides — not tokens.
- Code blocks use a hardcoded Catppuccin-Mocha palette in `index.css`. Flagged as drift candidate in [tokens-review.md — §Low-confidence drift](./tokens-review.md#low-confidence-drift-candidates-for-new-tokens).

## Motion (0)

No `--motion-*` or `--duration-*` variables. Motion is expressed as per-feature `@keyframes` with inline duration and `cubic-bezier()`.

**Keyframes defined in `ui/src/index.css`:**

| Name | Used by | Duration / easing |
|---|---|---|
| `dashboard-activity-enter` | `.activity-row-enter` | 520ms `cubic-bezier(0.16, 1, 0.3, 1)` |
| `dashboard-activity-highlight` | `.activity-row-enter` | 920ms `cubic-bezier(0.16, 1, 0.3, 1)` |
| `cot-line-slide-in` | `.cot-line-enter` | 300ms `cubic-bezier(0.4, 0, 0.2, 1)` |
| `cot-line-slide-out` | `.cot-line-exit` | 300ms `cubic-bezier(0.4, 0, 0.2, 1)` |
| `shimmer-text-slide` | `.shimmer-text` | 2.5s linear infinite |

Two easing curves recur (`cubic-bezier(0.16, 1, 0.3, 1)` and `cubic-bezier(0.4, 0, 0.2, 1)`). No duration pattern repeats. All animations honor `prefers-reduced-motion`.

## Elevation (0)

No `--shadow-*` tokens. The project's default visual elevation is border-based. Arbitrary `shadow-[…]` values appear in polished surfaces and UxLab prototypes; see [tokens-review.md — §Medium-confidence drift](./tokens-review.md#medium-confidence-drift).

## Out of scope / excluded from tokens.json

- **MDXEditor theme bridge** — 24 variables at `.paperclip-mdxeditor-scope, .paperclip-mdxeditor` (`index.css:332–361`). Every value is `var(--host-token)` or `color-mix(in oklab, var(--host-token) N%, …)` — a deliberate integration-layer alias, not a hardcoded theme. Documented as a bridge in [tokens-review.md — §Integration layer](./tokens-review.md#integration-layer-not-drift).
- **Scrollbar oklch values** — `index.css:172–219`. Hand-picked greys for light/dark scrollbar track/thumb. Candidate for tokenization; not done yet.
- **Component-local vars** — `.shimmer-text` (`--shimmer-base`, `--shimmer-highlight`) and `.paperclip-mermaid*` classes.
- **Hardcoded chart hex palette** in `ActivityCharts.tsx` / `OrgChart.tsx` — treated as drift, not as tokens. See [tokens-review.md](./tokens-review.md).
- **Raw Tailwind palette usage** across the codebase — treated as non-semantic drift. See [tokens-review.md — §Non-semantic color usage](./tokens-review.md#non-semantic-color-usage).
