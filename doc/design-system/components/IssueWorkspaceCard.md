# IssueWorkspaceCard

`ui/src/components/IssueWorkspaceCard.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 3 imports (1 pages, 2 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 522 lines

## Props

### `IssueWorkspaceCardProps`

```ts
issue: Omit<
Pick<
Issue,
| "companyId"
| "projectId"
| "projectWorkspaceId"
| "executionWorkspaceId"
| "executionWorkspacePreference"
| "executionWorkspaceSettings"
>,
"companyId"
> & {
companyId: string | null;
currentExecutionWorkspace?: ExecutionWorkspace | null;
```

## Composes

- **Primitives:** [button](./Button.md), [skeleton](./Skeleton.md)

## Used by

- **Pages:** `IssueDetail`
