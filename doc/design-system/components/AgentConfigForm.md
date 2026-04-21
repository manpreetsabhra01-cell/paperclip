# AgentConfigForm

`ui/src/components/AgentConfigForm.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 5 imports (3 pages, 0 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 1403 lines

## Props

### `AgentConfigFormProps`

```ts
adapterModels?: AdapterModel[];
onDirtyChange?: (dirty: boolean) => void;
onSaveActionChange?: (save: (() => void) | null) => void;
onCancelActionChange?: (cancel: (() => void) | null) => void;
hideInlineSave?: boolean;
showAdapterTypeField?: boolean;
showAdapterTestEnvironmentButton?: boolean;
showCreateRunPolicySection?: boolean;
hideInstructionsFile?: boolean;
/** Hide the prompt template field from the Identity section (used when it's shown in a separate Prompts tab). */
hidePromptTemplate?: boolean;
/** "cards" renders each section as heading + bordered card (for settings pages). Default: "inline" (border-b dividers). */
sectionLayout?: "inline" | "cards";
```

## Composes

- **Primitives:** [button](./Button.md), [popover](./Popover.md)

## Used by

- **Pages:** `AgentDetail`, `CompanyImport`, `NewAgent`
