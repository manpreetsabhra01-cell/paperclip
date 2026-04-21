# IssueRow

`ui/src/components/IssueRow.tsx`

[INFER] Standalone component. No file-level docstring.

## Quick facts

- **Category:** `standalone`
- **Usage:** 3 imports (1 pages, 2 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 169 lines

## Props

### `IssueRowProps`

```ts
issue: Issue;
issueLinkState?: unknown;
selected?: boolean;
mobileLeading?: ReactNode;
desktopMetaLeading?: ReactNode;
desktopLeadingSpacer?: boolean;
mobileMeta?: ReactNode;
desktopTrailing?: ReactNode;
trailingMeta?: ReactNode;
titleSuffix?: ReactNode;
unreadState?: UnreadState | null;
onMarkRead?: () => void;
onArchive?: () => void;
archiveDisabled?: boolean;
className?: string;
```

## Used by

- **Pages:** `Inbox`
