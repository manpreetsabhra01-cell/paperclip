# AgentIconPicker

`ui/src/components/AgentIconPicker.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 13 imports (4 pages, 9 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 81 lines
- **Sibling exports:** AgentIcon

## Props

### `AgentIconProps`

```ts
icon: string | null | undefined;
className?: string;
```

### `AgentIconPickerProps`

```ts
value: string | null | undefined;
onChange: (icon: string) => void;
children: React.ReactNode;
```

## Composes

- **Primitives:** [input](./Input.md), [popover](./Popover.md)

## Used by

- **Pages:** `AgentDetail`, `OrgChart`, `RoutineDetail`, `Routines`
