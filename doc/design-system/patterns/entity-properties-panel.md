# Entity Properties Panel

Side-panel content that shows an entity's metadata, lets the user edit inline, and drives per-field save state.

**Instances: 4 entity-specific panels + 1 generic panel.**
`AgentProperties`, `GoalProperties`, `IssueProperties`, `ProjectProperties` + `PropertiesPanel`.

> **Extraction-only pass.** This pattern does not prescribe a merge. The open question about whether `PropertiesPanel` composes or duplicates the four entity-specific panels is surfaced — not resolved. See [components-review.md §Likely duplicates #2](../components/components-review.md#2-properties-panel-family-agentproperties--goalproperties--issueproperties--projectproperties--generic-propertiespanel).

## Instances

| Component | Lines | Uses | Role |
|---|---|---|---|
| `PropertiesPanel.tsx` | 29 | 1 | **Generic chrome.** A slide-in `<aside>` that reads `panelContent` from `usePanel()` context and renders whatever the caller has set. |
| `AgentProperties.tsx` | (unread) | **0** | Entity-specific body (currently unused in production — Storybook-only). |
| `GoalProperties.tsx` | (unread) | 1 | Entity-specific body. |
| `IssueProperties.tsx` | 1370 | 2 | Entity-specific body; imports `StatusIcon`, `PriorityIcon`, `Identity`, `IssueReferencePill`, plus form primitives. |
| `ProjectProperties.tsx` | 1140 | 1 | Entity-specific body; imports `StatusBadge`, status-colors consumers, `InlineEditor`, `EnvVarEditor`. |

## Relationship: chrome vs content

Reading `PropertiesPanel.tsx` (29 lines):

```tsx
export function PropertiesPanel() {
  const { panelContent, panelVisible, setPanelVisible } = usePanel();
  if (!panelContent) return null;
  return (
    <aside className="… bg-card …">
      <div className="… flex flex-col …">
        <Header><Button icon="X" onClick={close} /></Header>
        <ScrollArea><div className="p-4">{panelContent}</div></ScrollArea>
      </div>
    </aside>
  );
}
```

So `PropertiesPanel` is a **slot**, not a content template. The four `*Properties` components are **contents** that get passed into that slot via `usePanel().setPanelContent(...)`.

## Open question (not resolved here)

Does `PropertiesPanel` already compose the four entity-specific panels, or do the four duplicate work that `PropertiesPanel` could own?

Evidence either way from a static read:

- **For "composes"**: the four entity-specific panels don't render a dialog/drawer wrapper themselves — each emits just the body content. They rely on some parent (either `PropertiesPanel` or a page-owned slot) to provide the outer container.
- **For "duplicates"**: the header layout (`Properties` title + close button) is in `PropertiesPanel`, but each of the four is 1100+ lines of its own scaffolding (section headers, separators, form fields, save-state handling) that *could* be factored into a `<PropertiesPanelBody>` helper.

**Not a call to make in this extraction.** The founder should open `IssueProperties.tsx` and `ProjectProperties.tsx` side-by-side and judge.

## Also noted

- `AgentProperties.tsx` has **0 production uses** (Storybook-only). Either abandoned or waiting for a page. See [components-review.md §Unused components](../components/components-review.md#unused--low-signal-components).
- Each entity-specific panel consumes `status-colors.ts` for status-color rendering (directly or via `StatusBadge`/`StatusIcon`), inheriting the token drift from [tokens-review.md §4](../tokens/tokens-review.md#4-status-colorsts-is-a-canonical-semantic-color-catalog-that-bypasses-the-ds).
- File sizes (1100–1370 lines each) suggest each panel handles its own save pipeline, field-level error states, mutations, and recent-selection tracking (indirectly observed via imports from `recent-assignees`, `recent-projects`, etc.). Whether that logic is shareable is the real design question underneath the styling question.

## Related components and patterns

- Chrome slot: `PropertiesPanel` and the [`usePanel()` context](../../../ui/src/context/PanelContext.tsx)
- Status coloring: [status-display.md](./status-display.md)
- Inline field editing primitives in [`agent-config-primitives.tsx`](../components/agent-config-primitives.md) (`DraftInput`, `InlineField`, `ToggleField`, `ToggleWithNumber`)
