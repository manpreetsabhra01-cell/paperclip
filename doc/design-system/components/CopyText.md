# CopyText

`ui/src/components/CopyText.tsx`

[INFER] Standalone component. No file-level docstring.

## Quick facts

- **Category:** `standalone`
- **Usage:** 3 imports (2 pages, 1 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 88 lines

## Props

### `CopyTextProps`

```ts
text: string;
/** What to display. Defaults to `text`. */
children?: React.ReactNode;
containerClassName?: string;
className?: string;
ariaLabel?: string;
title?: string;
/** Tooltip message shown after copying. Default: "Copied!" */
copiedLabel?: string;
```

## Used by

- **Pages:** `AgentDetail`, `ExecutionWorkspaceDetail`
