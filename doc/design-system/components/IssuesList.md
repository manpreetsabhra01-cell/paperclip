# IssuesList

`ui/src/components/IssuesList.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 6 imports (5 pages, 1 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 1170 lines

## Props

### `IssuesListProps`

```ts
issues: Issue[];
isLoading?: boolean;
error?: Error | null;
agents?: Agent[];
projects?: ProjectOption[];
liveIssueIds?: Set<string>;
projectId?: string;
viewStateKey: string;
issueLinkState?: unknown;
initialAssignees?: string[];
initialWorkspaces?: string[];
initialSearch?: string;
searchFilters?: Omit<IssueListRequestFilters, "q" | "projectId" | "limit" | "includeRoutineExecutions">;
baseCreateIssueDefaults?: Record<string, unknown>;
createIssueLabel?: string;
enableRoutineVisibilityFilter?: boolean;
onSearchChange?: (search: string) => void;
onUpdateIssue: (id: string, data: Record<string, unknown>) => void;
```

## Composes

- **Primitives:** [button](./Button.md), [collapsible](./Collapsible.md), [input](./Input.md), [popover](./Popover.md)

## Used by

- **Pages:** `ExecutionWorkspaceDetail`, `IssueDetail`, `Issues`, `ProjectDetail`, `Routines`
