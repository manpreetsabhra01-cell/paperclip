# PageTabBar

`ui/src/components/PageTabBar.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 10 imports (9 pages, 1 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 46 lines

## Props

### `PageTabBarProps`

```ts
items: PageTabItem[];
value?: string;
onValueChange?: (value: string) => void;
align?: "center" | "start";
```

## Composes

- **Primitives:** [tabs](./Tabs.md)

## Used by

- **Pages:** `AgentDetail`, `Agents`, `Approvals`, `Costs`, `ExecutionWorkspaceDetail` … (+4 more)
- **Components:** `CompanySettingsNav`
