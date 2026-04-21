# Detail Page

Full-page layout for viewing and editing a single entity (agent, issue, project, goal, routine, approval, or execution/project workspace).

**Instances: 8.** `AgentDetail`, `IssueDetail`, `ProjectDetail`, `GoalDetail`, `RoutineDetail`, `ApprovalDetail`, `ExecutionWorkspaceDetail`, `ProjectWorkspaceDetail`.

## Composition (shared baseline)

Measured across the 8 instances by import intersection:

- **`button`** — 8/8 (every detail page has actions in its header)
- **`tabs`** — 6/8 (detail pages split sub-views by tab)
- **`PageSkeleton`** — 5/8 (loading state while the entity is being fetched)
- **`StatusBadge`** — 4/8 (status is surfaced in the header area)
- **`separator`** — 4/8
- Breadcrumb context (`useBreadcrumbs`) — all 8 set a breadcrumb trail for the entity.

[INFER] Structural template, from reading AgentDetail and IssueDetail:

```
<PageSkeleton or <ContentLoaded>
  <Breadcrumb / back-nav>
  <Header>
    <Title>           — entity name + identifier
    <StatusBadge>     — where applicable (issue, run, approval, agent)
    <Actions>         — edit, archive, more-menu
  </Header>
  <Tabs>              — 2–5 tabs (overview, config, activity, …)
    <TabContent>
      … entity-specific body (properties, related work, charts, transcripts)
    </TabContent>
  </Tabs>
</>
```

## Canonical instance

`ui/src/pages/IssueDetail.tsx` is the most mature and most-cross-referenced implementation. `ui/src/pages/AgentDetail.tsx` is second and shows the tab-bar with many sub-surfaces.

## Variance across instances

Observed differences that may be intentional (different entity domain) or may be drift:

| Instance | Breadcrumbs | Tabs | Status element | Loading state | Notes |
|---|---|---|---|---|---|
| `IssueDetail` | yes | yes | `StatusIcon` + `StatusBadge` | custom | largest file; widely-referenced |
| `AgentDetail` | yes | yes | `StatusBadge` + `agentStatusDot` | `PageSkeleton` | composes `AgentConfigForm`, `ActivityCharts`, `PackageFileTree`, `RunTranscriptView` |
| `ProjectDetail` | yes | yes | `StatusBadge` | `PageSkeleton` | |
| `GoalDetail` | yes | (unconfirmed) | `StatusBadge` | `PageSkeleton` | |
| `RoutineDetail` | yes | yes | (unconfirmed) | `PageSkeleton` | |
| `ApprovalDetail` | yes | (none) | tone-coded via `ApprovalCard` | `PageSkeleton` | diverges — simpler shape |
| `ExecutionWorkspaceDetail` | yes | — | — | — | newer; diverges |
| `ProjectWorkspaceDetail` | yes | — | — | — | newer; diverges |

- **3 of 8 don't use `PageSkeleton`** — worth confirming each has an equivalent loading state.
- **`ApprovalDetail` skips tabs** — likely correct for its single-surface nature.
- **Status element is inconsistent** — `StatusBadge` alone, `StatusIcon + StatusBadge`, `StatusBadge + agentStatusDot` (separate dot helper), `ApprovalCard` tone encoding. Tied to [status-display.md](./status-display.md) and [../tokens/tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds).

## Related components and patterns

- Loading state: [`PageSkeleton`](../components/index.md) (documented as a component — used in 22+ places)
- Empty state: [`EmptyState`](../components/index.md) (mostly used on list pages)
- Status badge: [`StatusBadge`](../components/StatusBadge.md), [`StatusIcon`](../components/index.md) — see [status-display.md](./status-display.md)
- Tab bar: shadcn `tabs`, and a custom [`PageTabBar`](../components/PageTabBar.md) used on some pages

## Open questions / risks

- Whether to codify a `<DetailPageHeader>` composite (title + status + actions block) to reduce per-page drift. Four detail pages already diverge on which status element they use.
- The new `*WorkspaceDetail` pages do not yet share much structure with the older four. Check before Stage-4 pattern extraction runs again in a future quarter.
