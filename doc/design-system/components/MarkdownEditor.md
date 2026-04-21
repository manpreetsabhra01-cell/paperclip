# MarkdownEditor

`ui/src/components/MarkdownEditor.tsx`

[INFER] Standalone component. No file-level docstring.

## Quick facts

- **Category:** `standalone`
- **Usage:** 16 imports (5 pages, 9 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 1204 lines

## Props

### `MarkdownEditorProps`

```ts
value: string;
onChange: (value: string) => void;
placeholder?: string;
className?: string;
contentClassName?: string;
onBlur?: () => void;
imageUploadHandler?: (file: File) => Promise<string>;
/** Called when a non-image file is dropped onto the editor (e.g. .zip). */
onDropFile?: (file: File) => Promise<void>;
bordered?: boolean;
/** List of mentionable entities. Enables @-mention autocomplete. */
mentions?: MentionOption[];
/** Called on Cmd/Ctrl+Enter */
onSubmit?: () => void;
/** Render the rich editor without allowing edits. */
readOnly?: boolean;
```

## Used by

- **Pages:** `AgentDetail`, `CompanySkills`, `IssueDetail`, `RoutineDetail`, `Routines`
