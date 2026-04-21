# WorkspaceRuntimeControls

`ui/src/components/WorkspaceRuntimeControls.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 3 imports (2 pages, 1 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 454 lines

## Props

### `WorkspaceRuntimeControlsProps`

```ts
sections: WorkspaceRuntimeControlSections;
items?: never;
isPending?: boolean;
pendingRequest?: WorkspaceRuntimeControlRequest | null;
serviceEmptyMessage?: string;
jobEmptyMessage?: string;
emptyMessage?: never;
disabledHint?: string | null;
onAction: (request: WorkspaceRuntimeControlRequest) => void;
className?: string;
square?: boolean;
```

## Composes

- **Primitives:** [button](./Button.md)

## Used by

- **Pages:** `ExecutionWorkspaceDetail`, `ProjectWorkspaceDetail`
