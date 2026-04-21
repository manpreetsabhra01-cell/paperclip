# Token Review

This is the high-value artifact from Stage 1. It lists what looks wrong, inconsistent, or underdetermined about the token set. Ordered by expected human value, not by stage.

- **Generated:** 2026-04-21
- **Repo SHA:** a26e1288b627e82c554445732c7d844648e6b5e1
- **Scope:** `ui/` (`@paperclipai/ui`)
- **Inventory:** [tokens.md](./tokens.md), [tokens.json](./tokens.json)

---

## High-confidence drift (likely should be fixed)

### 1. `chart-*` tokens — reserved

**Status: RECLASSIFIED (2026-04-21).** Tokens preserved as **reserved** for the future chart-tokenization project. Not considered drift; do not consume today. Comment added above the `@theme` block in `ui/src/index.css`. Original finding retained below for history.

**Original finding:** `--chart-1` through `--chart-5` have **0 code usages** (excluding their definitions and the `DesignGuide.tsx` swatch showcase). Yet the app renders charts — `ui/src/components/ActivityCharts.tsx` and `ui/src/pages/OrgChart.tsx` carry the chart color logic.

**What they use instead:** raw Tailwind-palette hex values, typed directly into TSX.

```
ui/src/components/ActivityCharts.tsx:125-128
  critical: "#ef4444",  high: "#f97316",  medium: "#eab308",  low: "#6b7280",

ui/src/components/ActivityCharts.tsx:178-184
  todo: "#3b82f6",  in_progress: "#8b5cf6",  in_review: "#a855f7",
  done: "#10b981",  blocked: "#ef4444",  cancelled: "#6b7280", backlog: "#64748b",

ui/src/components/ActivityCharts.tsx:258
  color = rate >= 0.8 ? "#10b981" : rate >= 0.5 ? "#eab308" : "#ef4444";

ui/src/pages/OrgChart.tsx:162-169
  running: "#22d3ee",  active: "#4ade80",  paused: "#facc15",
  idle: "#facc15",  error: "#f87171",  terminated: "#a3a3a3",
```

**Why it matters:** `--chart-1..5` were reserved by the shadcn default theme for "data visualization tokens", but the actual chart layer never migrated onto them. Chart color is currently a parallel system, bound to Tailwind palette + status semantics rather than to the DS.

**Suggested direction (not decided here):** Replace `chart-1..5` with a semantic status-token family (see §4) and have `ActivityCharts` / `OrgChart` consume those. Or: delete `chart-1..5` if there's no chart design language yet and note the gap.

### 2. `destructive-foreground` has a wrong light-mode value AND is unused

**Status: RESOLVED (2026-04-21).** Light-mode value corrected to `oklch(0.985 0 0)` to match the dark-mode pattern. `ui/src/index.css:63`. Zero-risk change (0 production consumers confirmed). Original finding retained below for history.

**Original finding:** The light-mode value of `--destructive-foreground` was `oklch(0.577 0.245 27.325)` — the same red as `--destructive` itself. That means light-mode destructive text rendered over a destructive background would be invisible. The dark-mode value `oklch(0.985 0 0)` (white) is correct.

```
ui/src/index.css:62  --destructive: oklch(0.577 0.245 27.325);
ui/src/index.css:63  --destructive-foreground: oklch(0.577 0.245 27.325);   ← likely bug
ui/src/index.css:97  --destructive: oklch(0.637 0.237 25.331);
ui/src/index.css:98  --destructive-foreground: oklch(0.985 0 0);             ← correct
```

**Why no one has noticed:** `--destructive-foreground` has **0 code usages**. Components using destructive color use `bg-destructive` with `text-destructive` or implicit foreground, never `text-destructive-foreground` on a destructive-filled surface. So the bug is masked by non-adoption.

**Suggested direction:** Either delete the token, or fix its light value (likely `oklch(0.985 0 0)`) and actually use it on destructive buttons / filled badges.

### 3. `sidebar-*` tokens — reserved

**Status: RECLASSIFIED (2026-04-21).** Tokens preserved as **reserved** for shadcn sidebar primitive compatibility in case that primitive is reintroduced. Not considered drift; do not consume today. Comment added above the `@theme` block in `ui/src/index.css`. Original finding retained below for history.

**Original finding:** All 8 sidebar tokens (`sidebar`, `sidebar-foreground`, `sidebar-primary`, `sidebar-primary-foreground`, `sidebar-accent`, `sidebar-accent-foreground`, `sidebar-border`, `sidebar-ring`) have **0 code usages**. Only references are the `@theme inline` alias block and the `DesignGuide.tsx` swatch showcase.

```
rg -P "bg-sidebar|text-sidebar|border-sidebar|--sidebar"  ui/src
  → only hits in ui/pages/DesignGuide.tsx and ui/src/index.css
```

**Why:** The shadcn `Sidebar` primitive was not installed (no `ui/src/components/ui/sidebar.tsx`). The app has a custom `ui/src/components/Sidebar.tsx` that consumes the general semantic tokens (`background`, `accent`, `border`, etc.) directly. The `sidebar-*` family came from the shadcn default `components.json` theme generation and was never adopted.

**Suggested direction:** Either (a) delete the `sidebar-*` family from `index.css`, or (b) refactor `Sidebar.tsx` to consume them so the sidebar can be themed independently of the main surface. Status quo is dead code.

Note: in light mode, every `sidebar-*` value equals a semantic-surface value except the first — `sidebar` = `oklch(0.985 0 0)` vs `background` = `oklch(1 0 0)`. So consolidation would be nearly free if option (a) is chosen.

### 4. `status-colors.ts` is a canonical semantic-color catalog that bypasses the DS

**Status: PARTIALLY ADDRESSED (2026-04-21).** Two new action-severity tokens added — `--signal-success` and `--signal-success-foreground` — paired with the existing `--destructive` / `--destructive-foreground` as the DS's solid-accent severity vocabulary. `status-colors.ts` itself is **not touched** in this pass; tokenizing its entity-state coloring as a `--status-*` family is a deferred future project. `--signal-warning` and `--signal-info` are intentionally not added (see §Deferred signal variants below). Original finding retained below for history.

**Separation of concerns now documented.** `destructive` / `signal-success` = **action severity** (solid buttons, toasts). `status-colors.ts` = **entity state** (issue status, agent status, priority) — stays as a TypeScript catalog. `--chart-*` and `--sidebar-*` remain reserved (see §1, §3).

**Original finding:**

**Finding:** `ui/src/lib/status-colors.ts` defines the color language for all entity statuses and priorities (issues, agents, goals, runs, approvals) and then renders them with **raw Tailwind palette classes**. Every one of ~24 status/priority entries looks like:

```ts
// ui/src/lib/status-colors.ts (excerpt)
issueStatusIcon.todo        = "text-blue-600 border-blue-600 dark:text-blue-400 dark:border-blue-400"
statusBadge.running         = "bg-cyan-100 text-cyan-700 dark:bg-cyan-900/50 dark:text-cyan-300"
statusBadge.pending_approval = "bg-amber-100 text-amber-700 dark:bg-amber-900/50 dark:text-amber-300"
priorityColor.critical      = "text-red-600 dark:text-red-400"
agentStatusDot.running      = "bg-cyan-400 animate-pulse"
```

The file header says _"Every component that renders a status indicator … should import from here so colors stay consistent"_ — i.e. status color IS treated as part of the design system, but it lives in TypeScript, not in CSS tokens, and uses raw Tailwind scales rather than semantic tokens.

**Hues in active status use:** blue, cyan, sky, green, emerald, amber, orange, yellow, violet, red, neutral. That's 11 hues — more than the shadcn default palette anticipates.

**Why it matters:** This is the single largest DS gap found in Stage 1. A token family like `--signal-success`, `--signal-warning`, `--signal-error`, `--signal-info`, `--signal-in-progress`, `--signal-review`, `--signal-neutral` (each with `-bg`, `-text`, `-border` variants) would unify the status color language, make dark-mode pairing automatic, and close the drift feeding into `ActivityCharts.tsx` (§1).

**Suggested direction:** Design a signal/status token family and migrate `status-colors.ts` onto it. Do this **before** Stage 3 extracts `StatusBadge`, `StatusIcon`, `PriorityIcon`, `AgentStatusDot` — otherwise those component docs will bake in the raw-palette drift.

### 5. Theme-color meta tag uses hardcoded hex

```
ui/src/context/ThemeContext.tsx:20  const DARK_THEME_COLOR = "#18181b";
ui/src/context/ThemeContext.tsx:21  const LIGHT_THEME_COLOR = "#ffffff";
```

These drive the browser's `<meta name="theme-color">` tag so the mobile browser chrome matches the app. They're sort-of the inverse of `--background`:

- Light: `--background = oklch(1 0 0) = #ffffff` — the hardcoded value matches.
- Dark: `--background = oklch(0.145 0 0) ≈ #252525`, but the hardcoded is `#18181b` (zinc-900). **Mismatch.**

**Suggested direction:** Compute from `--background` at runtime, or keep the hardcoded values but make them match `--background` exactly. Currently the mobile chrome is a different dark than the app's background.

---

## Medium-confidence drift

### 6. Raw Tailwind palette usage: 659 occurrences across 83 files

The codebase bypasses semantic tokens for a large number of styling decisions:

```
rg -P "\b(bg|text|border|...)-(neutral|gray|zinc|slate|stone|red|orange|amber|yellow|lime|green|emerald|teal|cyan|sky|blue|indigo|violet|purple|fuchsia|pink|rose)-(50|100|…|950)\b" ui/src
  → 659 occurrences across 83 files
```

**Heavy offenders (ranked):**

| File | Hits | Notes |
|---|---|---|
| `ui/src/pages/AgentDetail.tsx` | 75 | Production page — worth migrating. |
| `ui/src/pages/InviteLanding.tsx` | 58 | Auth surface with zinc-based dark palette; deliberately different visual language than the app. |
| `ui/src/pages/InviteUxLab.tsx` | 55 | UX lab prototype. Acceptable scratch. |
| `ui/src/lib/status-colors.ts` | 47 | See §4. |
| `ui/src/components/transcript/RunTranscriptView.tsx` | 47 | Feature uses palette for agent action differentiation — tokenize. |
| `ui/src/components/IssueChatThread.tsx` | 22 | Production component. |
| `ui/src/pages/Inbox.tsx` | 16 | |
| `ui/src/pages/DesignGuide.tsx` | 13 | Swatch page — presentational, acceptable. |
| `ui/src/pages/IssueChatUxLab.tsx` | 6 | UX lab. |
| `ui/src/pages/RunTranscriptUxLab.tsx` | 10 | UX lab. |

**Buckets:**
- **Clearly intentional scratch / auth surface:** UX Labs (`IssueChatUxLab`, `InviteUxLab`, `RunTranscriptUxLab`), `InviteLanding` — probably should stay.
- **Status color catalog:** `status-colors.ts` + `ActivityCharts.tsx` — covered by §4, §1.
- **Production surfaces that silently diverge:** `AgentDetail.tsx`, `RunTranscriptView.tsx`, `IssueChatThread.tsx`, `Inbox.tsx` — highest ROI to migrate.

### 7. Arbitrary radius values bypass the scale (18 occurrences)

```
ui/src/pages/InviteUxLab.tsx           rounded-[28px], rounded-[24px], rounded-[32px]
ui/src/pages/IssueChatUxLab.tsx        rounded-[28px], rounded-[32px]
ui/src/pages/RunTranscriptUxLab.tsx    rounded-xl (via theme), rounded-2xl (via TW default)
ui/src/pages/ProfileSettings.tsx       rounded-[28px], rounded-[24px]
ui/src/pages/CompanySettings.tsx       rounded-[14px]
ui/src/components/CompanyRail.tsx      rounded-[14px], rounded-[22px]
```

These are almost all in pages that already opt out of the default radius scale. See §Radius scale below for the likely reason.

### 8. Project/label color fallbacks fragmented across 10+ files

Two default fallback hexes for user-picked project/label colors:

- `#6366f1` (indigo-500) — in `IssueProperties.tsx`, `SidebarProjects.tsx`, `NewIssueDialog.tsx`, `ProjectDetail.tsx`, `CompanySettings.tsx`
- `#64748b` (slate-500) — in `IssueColumns.tsx`, `RoutineRunVariablesDialog.tsx`, `RoutineDetail.tsx`, `Routines.tsx`, `MarkdownEditor.tsx`

Not strictly drift — these are fallbacks for a user-supplied field, not the DS. But they disagree with each other, and every caller has duplicated the literal. A single `const DEFAULT_PROJECT_COLOR` (or better, a DS token + utility) would fix it.

### 9. Arbitrary shadow values in production surfaces

```
shadow-[0_24px_60px_rgba(15,23,42,0.08)]       UxLab pages, ProfileSettings
shadow-[0_20px_80px_-40px_rgba(0,0,0,0.55)]    BudgetPolicyCard
shadow-[0_0_12px_rgba(6,182,212,0.08)]         AgentDetail (live indicator)
shadow-[0_18px_50px_rgba(6,182,212,0.08)]      LiveRunWidget
```

No `--shadow-*` tokens exist, so there's nothing to migrate _to_, but these are the signal that a shadow/elevation token family would pay for itself quickly.

---

## Low-confidence drift (candidates for new tokens)

### 10. Code block theme is a hardcoded Catppuccin Mocha palette

```
ui/src/index.css:553   background: #1e1e2e;         ← code bg
ui/src/index.css:554   color: #cdd6f4;              ← code fg
ui/src/index.css:567   background-color: #181825;   ← gutter bg
ui/src/index.css:568   color: #585b70;              ← gutter fg
ui/src/index.css:569   border-right: 1px solid #313244;
ui/src/index.css:614   background-color: #313244;   ← language selector bg
ui/src/index.css:616   border-color: #45475a;
ui/src/index.css:586   color-mix(…, #89b4fa 25%, transparent)  ← selection
```

This is a deliberate choice (Catppuccin Mocha is a widely-loved dev theme and it's consistent across MDXEditor + rendered markdown + CodeMirror). But the choice is buried in selectors and spread across 30+ lines of `index.css`. Candidate `--code-bg`, `--code-fg`, `--code-gutter-bg`, `--code-gutter-fg`, `--code-border`, `--code-selection` tokens would:

- Make the choice legible.
- Make "port to Catppuccin Latte for light mode" a one-line change.
- Let MDXEditor and the `.paperclip-markdown` renderer reference the same tokens rather than copy the values.

### 11. GitHub link colors hardcoded

```
ui/src/index.css:429  color: color-mix(in oklab, var(--foreground) 76%, #0969da 24%);
ui/src/index.css:436  color: color-mix(in oklab, var(--foreground) 80%, #58a6ff 20%);  (.dark)
ui/src/index.css:756  (duplicated for .paperclip-markdown)
ui/src/index.css:772  (duplicated for .paperclip-markdown dark)
```

Candidate `--link` / `--link-foreground` tokens.

### 12. MDXEditor editor text colors hardcoded

The CodeMirror-inside-MDXEditor rules (`index.css:560–618`) hardcode `#cdd6f4`, `#89b4fa`, `#313244` etc. for cursor, selection, gutter. Covered by §10 if `--code-*` tokens are added.

---

## Reserved & near-dead tokens

Summary of tokens whose production usage is at or near zero. **As of 2026-04-21, the 13 previously-flagged "dead" tokens are reclassified as Reserved.** They remain defined in `ui/src/index.css` with explanatory comments; they are not a consolidation opportunity.

| Token | Code usage | Disposition |
|---|---|---|
| `chart-1..5` | 0 each | **Reserved** for future chart tokenization (see §1) |
| `sidebar`, `sidebar-foreground`, `sidebar-primary`, `sidebar-primary-foreground`, `sidebar-accent`, `sidebar-accent-foreground`, `sidebar-border`, `sidebar-ring` | 0 each | **Reserved** for shadcn sidebar compatibility (see §3) |
| `destructive-foreground` | 0 | Unused but light value corrected on 2026-04-21 (see §2). Paired with `--destructive` and with the new `--signal-success-foreground`; kept as part of the action-severity vocabulary. |
| `card-foreground` | 1 | Near-dead. Not necessarily wrong — `card-foreground` equals `foreground` in both modes, so `text-foreground` inside a card is sufficient. |
| `secondary-foreground` | 2 | Near-dead |
| `secondary` | 3 | Near-dead |
| `popover-foreground` | 5 | Low use — likely limited to `popover.tsx` primitive |

No longer flagged as a consolidation opportunity. The 34-token color set is the intended surface.

---

## Duplicate values

These groups of tokens share identical values. Most are classic shadcn-default structural overlaps (intentional, so `*-foreground` patterns match across surfaces). A few are suspicious.

**Light mode:**

| Value | Tokens |
|---|---|
| `oklch(1 0 0)` | `background`, `card`, `popover` |
| `oklch(0.145 0 0)` | `foreground`, `card-foreground`, `popover-foreground`, `sidebar-foreground` |
| `oklch(0.205 0 0)` | `primary`, `secondary-foreground`, `accent-foreground`, `sidebar-primary` |
| `oklch(0.985 0 0)` | `primary-foreground`, `sidebar`, `sidebar-primary-foreground`, `sidebar-accent-foreground` |
| `oklch(0.97 0 0)` | `secondary`, `muted`, `accent`, `sidebar-accent` |
| `oklch(0.922 0 0)` | `border`, `input`, `sidebar-border` |
| `oklch(0.708 0 0)` | `ring`, `sidebar-ring` |
| `oklch(0.577 0.245 27.325)` | `destructive`, `destructive-foreground` ⚠ (see §2) |

**Dark mode** (analogous structure — all sidebar tokens match their non-sidebar counterparts except `sidebar-primary = oklch(0.488 0.243 264.376)`, a distinct blue).

**Takeaway:** In light mode, every `sidebar-*` value equals its non-sidebar counterpart; in dark mode, `sidebar-primary` drifts (blue) from `primary` (white). Per the 2026-04-21 decision, the sidebar family is **preserved as reserved** (see §3) rather than collapsed — the identity with non-sidebar tokens is fine; the value of reserving is keeping the option of theming the sidebar independently if that need emerges.

---

## Non-semantic color usage

(Covered by §6 above and enumerated there with offender files and hit counts.)

---

## Radius scale

**Status: RESOLVED (2026-04-21).** Scale restored to monotonic `sm=6px, md=8px, lg=10px, xl=12px`. 226 call sites in `ui/src/` migrated from `rounded-lg` / `rounded-xl` → `rounded-none` to preserve the existing flat-Swiss aesthetic on dashboard surfaces. Shadcn primitives (`ui/src/components/ui/**`) excluded from migration — `dialog.tsx` retains `rounded-lg` so `DialogContent` now renders with real 10px-rounded corners (the first observable visual change from the radius work, intentional). One test assertion updated in lockstep: `ProjectWorkspaceSummaryCard.test.tsx:135` (`rounded-lg` → `rounded-none`).

Original Stage 1 observations retained below for history.

### What's defined

```
ui/src/index.css:39   --radius-sm: 0.375rem;   (6px)        @theme inline
ui/src/index.css:40   --radius-md: 0.5rem;     (8px)        @theme inline
ui/src/index.css:41   --radius-lg: 0px;                     @theme inline
ui/src/index.css:42   --radius-xl: 0px;                     @theme inline
ui/src/index.css:47   --radius:    0;                       :root (not @theme)
```

### What's non-standard

1. **Non-monotonic progression.** Almost every design-system radius scale is `sm ≤ md ≤ lg ≤ xl`. Here it's `sm=6, md=8, lg=0, xl=0`. Two possibilities:
   - Intentional flattening at the outer scale (keep small things rounded for friendliness, keep large surfaces square for an editorial look).
   - Mid-migration artifact from flipping the scale to 0 from the shadcn defaults (`lg=0.5rem`, `xl=0.75rem`) and not yet reconciling.

2. **Two coexisting radius bases.** `--radius` (value 0) lives at `:root` and is referenced by `index.css`'s own `calc(var(--radius) - 2px)` expressions and by the MDXEditor bridge (`--baseRadius: var(--radius)`). But the Tailwind-visible scale is the `@theme`-scoped `--radius-sm/md/lg/xl`. These never cross-reference. An editor reading the file might reasonably expect `--radius-md` to be derived from `--radius`, but they're independent.

3. **Heavy use of utilities that resolve to 0.**

   | Utility | Value | Uses |
   |---|---|---|
   | `rounded-sm` | 0.375rem | 49 |
   | `rounded-md` | 0.5rem | 310 |
   | `rounded-lg` | **0px** | **146** |
   | `rounded-xl` | **0px** | **81** |
   | `rounded-2xl` | (Tailwind default, not themed) | 21 |
   | `rounded-full` | full | 208 |
   | `rounded-none` | 0 | 36 |
   | `rounded` (no suffix) | (Tailwind default) | 127 |

   So **227 places** in the codebase write `rounded-lg` or `rounded-xl` and get square corners. Without context on whether that's the intended look, this is ambiguous — if intentional, all those sites are fine; if unintentional, every one of them is a small visual bug.

4. **Arbitrary radius values (see §7).** 18 occurrences of `rounded-[14px]`, `rounded-[22px]`, `rounded-[24px]`, `rounded-[28px]`, `rounded-[32px]` — concentrated in pages that want actually-rounded corners. If `rounded-lg` resolved to ~12px, some of these would go away.

### Open question for the founder

_Is the radius scale intentionally flattening at the top (lg/xl = 0), or is that a stale state from earlier shadcn defaults that should be replaced with a monotonic scale?_

**Resolved 2026-04-21.** Hybrid: dashboard surfaces stay flat (`rounded-none`), the scale is restored monotonically for newer surfaces and for the `dialog.tsx` primitive. 226 host-code call sites migrated.

---

### Radius workaround audit: 18 occurrences, all retained

Per the decision to audit each `rounded-[Npx]` workaround: none match the new scale (6 / 8 / 10 / 12 px). All are intentional. Table below captures the disposition.

| File | Line | Value | Context | Disposition |
|---|---|---|---|---|
| `ui/src/pages/IssueChatUxLab.tsx` | 52 | `rounded-[28px]` | Chat-surface outer container (prototype) | **Keep** — editorial pill > xl scale; UX Lab prototype |
| `ui/src/pages/IssueChatUxLab.tsx` | 139 | `rounded-[32px]` | Hero container with layered gradients (prototype) | **Keep** — ditto |
| `ui/src/pages/InviteUxLab.tsx` | 112 | `rounded-[28px]` | Invite-panel outer (prototype) | **Keep** — UX Lab |
| `ui/src/pages/InviteUxLab.tsx` | 149 | `rounded-[24px]` | Tone card (prototype) | **Keep** — UX Lab |
| `ui/src/pages/InviteUxLab.tsx` | 171 | `rounded-[28px]` | Dark invite capsule (prototype) | **Keep** — UX Lab |
| `ui/src/pages/InviteUxLab.tsx` | 454 | `rounded-[28px]` | Alt invite panel (prototype) | **Keep** — UX Lab |
| `ui/src/pages/InviteUxLab.tsx` | 529 | `rounded-[28px]` | Tone card variant (prototype) | **Keep** — UX Lab |
| `ui/src/pages/InviteUxLab.tsx` | 612 | `rounded-[28px]` | Tone card variant (prototype) | **Keep** — UX Lab |
| `ui/src/pages/InviteUxLab.tsx` | 700 | `rounded-[32px]` | Hero gradient container (prototype) | **Keep** — UX Lab |
| `ui/src/pages/ProfileSettings.tsx` | 159 | `rounded-[28px]` | Profile header hero card | **Keep** — intentional oversize hero radius |
| `ui/src/pages/ProfileSettings.tsx` | 163 | `rounded-[24px]` | Profile identity panel | **Keep** — matches hero language |
| `ui/src/pages/CompanySettings.tsx` | 296 | `rounded-[14px]` | Brand-color preview swatch | **Keep** — matches CompanyRail icon scale |
| `ui/src/components/CompanyRail.tsx` | 99 | `rounded-[14px]` | Company icon (selected state) | **Keep** — iOS-icon-scale, morphing-radius pattern |
| `ui/src/components/CompanyRail.tsx` | 100 | `rounded-[22px]` | Company icon (unselected state) | **Keep** — part of the hover-morph pair |
| `ui/src/components/CompanyRail.tsx` | 247 | `rounded-[22px]` → `[14px]` on hover | "New company" button | **Keep** — same morph pattern |
| `ui/src/components/ui/checkbox.tsx` | 17 | `rounded-[4px]` | Checkbox control | **Keep** — shadcn primitive; excluded from migration |
| `ui/src/components/ui/tooltip.tsx` | 51 | `rounded-[2px]` | Tooltip arrow | **Keep** — shadcn primitive; excluded from migration |
| `ui/src/components/ui/scroll-area.tsx` | 19 | `rounded-[inherit]` | Scroll viewport | **Keep** — non-numeric special value; can't match the scale by construction |

Summary:
- **9 in UX Lab pages** (`InviteUxLab`, `IssueChatUxLab`) — prototypes, not pursued.
- **2 in ProfileSettings** — intentional editorial hero radii (24px / 28px).
- **1 in CompanySettings** — brand-swatch match to the rail.
- **3 in CompanyRail** — morphing-radius iOS-icon pattern (22px ↔ 14px on hover).
- **3 in shadcn primitives** — out of scope per the migration rule.

No rewrites. No consolidations. All values sit outside the new 6-8-10-12 scale either because they're editorial (20-30px range), because they're part of a morphing-radius interaction (14/22), or because they're shadcn-internal micro-radii (2/4 px, or `inherit`).

---

## Deferred signal variants

Captured here so they don't get forgotten. **Intentionally not added** in the 2026-04-21 pass:

- **`--signal-success-soft` / `--signal-success-subtle`** — a lower-weight variant for use on toast surfaces and inline success banners. Current soft-success rendering uses `bg-emerald-50 text-emerald-900` (light) and `bg-emerald-950/60 text-emerald-100` (dark) in [`ToastViewport.tsx`](../../../ui/src/components/ToastViewport.tsx) — a different hue family (emerald) than the new solid `--signal-success` (green-700/600). A soft variant would let the toast adopt the DS token without shifting visual weight. Add when a concrete refactor needs it.
- **`--signal-warning` / `--signal-info`** — not defined. No current consumer that couldn't go on using its local palette. Add when a real use case appears (e.g., a standardized warning toast variant).
- **Green vs emerald reconciliation.** Approve buttons use `green-700/600`; soft success surfaces use `emerald-50/900`. `--signal-success` sourced from the button family. If a soft-variant lands, the emerald surfaces become the migration candidate — visible as a small hue shift on the toast success background. Document the shift when migrating.

## Integration layer (not drift)

### MDXEditor CSS-variable bridge

**Lines:** `ui/src/index.css:332–361`. **24 variables**, scoped to `.paperclip-mdxeditor-scope, .paperclip-mdxeditor`.

These are not tokens. They are an explicit bridge between Paperclip's DS tokens and MDXEditor's internal token vocabulary. Every one of the 24 values is a `var(--host-token)` reference or a `color-mix()` over host tokens — no hardcoded color values. Documenting the mapping for traceability:

| MDXEditor var | Maps to |
|---|---|
| `--baseBase` | `var(--background)` |
| `--baseBg` | `transparent` |
| `--baseBgSubtle` | `color-mix(in oklab, var(--accent) 35%, transparent)` |
| `--baseLine` | `var(--border)` |
| `--baseSolid` | `var(--muted-foreground)` |
| `--baseSolidHover` | `var(--foreground)` |
| `--baseText` | `var(--muted-foreground)` |
| `--baseBorderColor` | `var(--border)` |
| `--baseBorder` | `var(--border)` |
| `--baseBorderHover` | `var(--ring)` |
| `--baseTextContrast` | `var(--foreground)` |
| `--baseTextContrastMuted` | `var(--muted-foreground)` |
| `--baseTextEmphasis` | `var(--foreground)` |
| `--basePageBg` | `var(--background)` |
| `--baseRadius` | `var(--radius)` (the `:root`-scoped 0-value, not a `@theme` radius) |
| `--baseLineHeight` | `1.5` (hardcoded numeric — the only non-bridge value in the block) |
| `--accentBorder` | `color-mix(in oklab, var(--primary) 35%, var(--border))` |
| `--accentSolid` | `var(--primary)` |
| `--accentSolidHover` | `var(--primary)` |
| `--accentLine` | `color-mix(in oklab, var(--primary) 20%, transparent)` |
| `--accentBg` | `var(--accent)` |
| `--accentBgHover` | `color-mix(in oklab, var(--accent) 80%, var(--background))` |
| `--accentBgActive` | `color-mix(in oklab, var(--accent) 72%, var(--background))` |
| `--accentText` | `var(--accent-foreground)` |

The bridge is sound. If DS tokens move, the editor moves with them. Nothing to do.

### Scrollbar oklch values

`index.css:172–219`. Hand-picked greys for light/dark scrollbar tracks and thumbs. Not tokens, but candidates for tokenization if scrollbar theming becomes a DS concern.
