# InlineEntitySelector

`ui/src/components/InlineEntitySelector.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 8 imports (2 pages, 4 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 215 lines

## Props

### `InlineEntitySelectorProps`

```ts
value: string;
options: InlineEntityOption[];
placeholder: string;
noneLabel: string;
searchPlaceholder: string;
emptyMessage: string;
onChange: (id: string) => void;
onConfirm?: () => void;
className?: string;
renderTriggerValue?: (option: InlineEntityOption | null) => ReactNode;
renderOption?: (option: InlineEntityOption, isSelected: boolean) => ReactNode;
recentOptionIds?: string[];
/** Skip the Portal so the popover stays in the DOM tree (fixes scroll inside Dialogs). */
disablePortal?: boolean;
/** Open the popover when the trigger receives keyboard/programmatic focus. */
openOnFocus?: boolean;
```

## Composes

- **Primitives:** [popover](./Popover.md)

## Used by

- **Pages:** `RoutineDetail`, `Routines`
