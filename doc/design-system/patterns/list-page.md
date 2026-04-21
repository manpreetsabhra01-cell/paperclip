# List Page

Full-page layout for browsing a collection of entities (agents, issues, projects, goals, routines, approvals, workspaces).

**Instances: 12.** `Activity`, `Agents`, `Approvals`, `Companies`, `Goals`, `Inbox`, `Issues`, `MyIssues`, `Projects`, `Routines`, `Workspaces`, `JoinRequestQueue`.

## Composition (shared baseline)

Measured across 12 instances by import intersection:

- **`PageSkeleton`** — 9/12 (the loading shell)
- **`EmptyState`** — 8/12 (shown when the collection is empty)
- **`button`** — 7/12 (action buttons: new-X, filters, view-toggle)
- **`PageTabBar`** — 4/12 (views split into named lists)
- **`tabs`** — 4/12

[INFER] Structural template, from reading Issues / Agents / Projects:

```
<Page>
  <Header>
    <Title>                         — "Issues", "Agents", …
    <FilterOrTabBar>                — PageTabBar or tabs for sub-collections
    <ActionButton>                  — "New Issue", "Invite", etc.
  </Header>
  <Body>
    {loading  ? <PageSkeleton />
     empty    ? <EmptyState title message action />
              : <List / Grid / Kanban>
                  {items.map(item =>
                    <IssueRow or EntityRow or domain-specific row />)}
                </>
    }
  </Body>
</Page>
```

## Canonical instance

No single clean instance — `Issues.tsx` and `Agents.tsx` are both representative but each mixes in substantial custom logic. `Agents.tsx` shows the pattern with the least domain-specific clutter.

## Variance across instances

- **Row rendering diverges.** `Issues` uses `IssueRow`, `Agents` builds a grid of cards, `Approvals` uses a list of cards, `Goals` uses `GoalTree`. Different collections legitimately need different affordances, but none of them use the generic `EntityRow` — which is 6 uses spread across feature subsurfaces, not pages.
- **Empty-state content is ad-hoc.** No shared "empty collection" copy or illustration; each page passes its own strings to `EmptyState`.
- **Sorting / filtering surface is not shared.** Some pages have a `FilterBar`, some have in-header popovers, some have `IssueFiltersPopover` specifically for Issues. See also [components-review.md §Naming inconsistencies](../components/components-review.md#naming-inconsistencies) on `*Bar` vs popover filters.
- **`Activity` is borderline.** It's a chronological feed rather than a collection — composes `ActivityRow` only.
- **Loading state absent on 3 pages** — confirm each has its own mechanism.

## Related components and patterns

- [`EmptyState`](../components/index.md) (used in 19 places across the app)
- [`PageSkeleton`](../components/index.md) (22 places)
- [`PageTabBar`](../components/PageTabBar.md) (10 uses)
- [entity-row pattern](./entity-row.md) — generic row, currently underused on list pages

## Open questions / risks

- `EntityRow` (6 uses, generic) is never used on the main list pages. The main list pages roll their own row. Worth asking whether that's by choice or by miss. See also [entity-row.md](./entity-row.md).
- The mobile list experience is handled per-page; no shared mobile list pattern found. See [`SwipeToArchive.tsx`](../components/index.md) which only `Inbox` uses.
