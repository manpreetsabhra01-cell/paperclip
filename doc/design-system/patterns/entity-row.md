# Entity Row

Row element for listing items in a scrollable collection (inbox, activity feed, list pages).

**Instances: 3.** `ActivityRow`, `EntityRow`, `IssueRow`.

> **Extraction-only pass.** Documents the family; does not prescribe the merge suggested in components-review. See [components-review.md §Likely duplicates #6](../components/components-review.md#6-row-family-activityrow--entityrow--issuerow).

## Instances

| Component | Lines | Uses | Role |
|---|---|---|---|
| `EntityRow.tsx` | 69 | 6 | Generic slot-based row (`leading` / `identifier` / `title` / `subtitle` / `trailing`) |
| `ActivityRow.tsx` | 92 | 2 | Activity-event-specific — renders an `ActivityEvent` with actor identity + action verb + entity link |
| `IssueRow.tsx` | 168 | 3 | Issue-specific — renders an `Issue` with `StatusIcon`, mobile/desktop slot variants, unread state, archive action |

## Composition

**`EntityRow`** — truly generic:

```tsx
interface EntityRowProps {
  leading?: ReactNode;
  identifier?: string;
  title: string;
  subtitle?: string;
  trailing?: ReactNode;
  selected?: boolean;
  to?: string;
  onClick?: () => void;
  className?: string;
}
```

Renders as `<Link>` or `<div>` depending on click-ability. No status, no unread state, no mobile/desktop split — just slots.

**`ActivityRow`** — imports `Identity`, `IssueReferenceActivitySummary`. Specific to activity events.

**`IssueRow`** — imports `StatusIcon`. Props interface has 15 fields, most of them optional `ReactNode` slots:

```ts
interface IssueRowProps {
  issue: Issue;
  issueLinkState?: unknown;
  selected?: boolean;
  mobileLeading?: ReactNode;       // slot
  desktopMetaLeading?: ReactNode;  // slot
  desktopLeadingSpacer?: boolean;
  mobileMeta?: ReactNode;          // slot
  desktopTrailing?: ReactNode;     // slot
  trailingMeta?: ReactNode;        // slot
  titleSuffix?: ReactNode;         // slot
  unreadState?: UnreadState | null;
  onMarkRead?: () => void;
  onArchive?: () => void;
  archiveDisabled?: boolean;
  className?: string;
}
```

Six slot props plus an untyped `issueLinkState` — this shape is nearly "`<EntityRow>` + issue-specific defaults."

## Observations

- `EntityRow` is used in 6 composites (not examined here — see composition graph), but **not used on any main list page**. Main list pages roll their own row rendering.
- `IssueRow` is only used in 2 places: the `Inbox` page and `SwipeToArchive`. Not used on the `Issues` page (which uses `IssueColumns` + custom row rendering per column).
- The gap is: `EntityRow` covers the "slot-based row" role generically, but the list pages don't adopt it.

## Variance

- **Mobile/desktop split lives only in `IssueRow`.** Whether other pages need it or have their own responsive handling is unknown from static analysis.
- **Unread state lives only in `IssueRow`.** Inbox-specific; would not generalize.
- **Activity-specific text (verb, link target) lives only in `ActivityRow`.** Legitimate domain specialization.

## Open questions

- Could `IssueRow` be expressed as `<EntityRow kind="issue" ... />`? Its slot shape already matches `EntityRow`'s role; the issue-specific bits (StatusIcon, unread state, archive) are add-ons, not structural differences.
- Why do the main list pages (`Issues`, `Agents`, `Projects`, …) avoid `EntityRow`? If there's a good reason it should be documented; if not, adoption would retire a lot of per-page row code.

Answers to these are not required for this extraction. The pattern is noted as documentation-relevant.
