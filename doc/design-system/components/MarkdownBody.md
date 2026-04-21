# MarkdownBody

`ui/src/components/MarkdownBody.tsx`

[INFER] Standalone component. No file-level docstring.

## Quick facts

- **Category:** `standalone`
- **Usage:** 11 imports (5 pages, 6 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 326 lines

## Props

### `MarkdownBodyProps`

```ts
children: string;
className?: string;
style?: React.CSSProperties;
softBreaks?: boolean;
linkIssueReferences?: boolean;
/** Optional resolver for relative image paths (e.g. within export packages) */
resolveImageSrc?: (src: string) => string | null;
/** Called when a user clicks an inline image */
onImageClick?: (src: string) => void;
```

## Used by

- **Pages:** `AgentDetail`, `ApprovalDetail`, `CompanyExport`, `CompanyImport`, `CompanySkills`
