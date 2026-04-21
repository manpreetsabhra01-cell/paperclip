# InlineEditor

`ui/src/components/InlineEditor.tsx`

[INFER] Standalone component. No file-level docstring.

## Quick facts

- **Category:** `standalone`
- **Usage:** 6 imports (4 pages, 2 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 310 lines

## Props

### `InlineEditorProps`

```ts
value: string;
onSave: (value: string) => void | Promise<unknown>;
as?: "h1" | "h2" | "p" | "span";
className?: string;
placeholder?: string;
multiline?: boolean;
imageUploadHandler?: (file: File) => Promise<string>;
/** Called when a non-image file is dropped onto the editor. */
onDropFile?: (file: File) => Promise<void>;
mentions?: MentionOption[];
nullable?: boolean;
```

## Used by

- **Pages:** `DesignGuide`, `GoalDetail`, `IssueDetail`, `ProjectDetail`
