# Entity-Creation Dialog

Dialog surface for creating a new entity (agent, goal, issue, project).

**Instances: 4.** `NewAgentDialog`, `NewGoalDialog`, `NewIssueDialog`, `NewProjectDialog`.

> **Extraction-only pass.** This pattern document records the family as it exists today. It does not prescribe a merge into a single generic `NewEntityDialog`. See [components-review.md §Likely duplicates #1](../components/components-review.md#1-entity-creation-dialog-family-newagentdialog--newgoaldialog--newissuedialog--newprojectdialog) for the open-question treatment.

## Instances

| File | Lines | Uses | Opened via |
|---|---|---|---|
| `ui/src/components/NewAgentDialog.tsx` | 210 | 1 | `useDialog().newAgentOpen` |
| `ui/src/components/NewGoalDialog.tsx` | (unread) | 1 | `useDialog()` |
| `ui/src/components/NewIssueDialog.tsx` | 1699 | 2 | `useDialog()` |
| `ui/src/components/NewProjectDialog.tsx` | (unread) | 1 | `useDialog()` |

## Composition (shared)

All four:

- Import `Dialog` + `DialogContent` from `@/components/ui/dialog` (primitive).
- Consume a central `useDialog()` context from `ui/src/context/DialogContext` that exposes open/close flags per entity type.
- Call an entity-specific API on submit (`agentsApi.create`, `issuesApi.create`, …) via `useMutation`.
- Dismiss via `closeNewX()` from the same context.

## Shape divergence

The instances are **not structurally equivalent.** Line counts alone:

- `NewAgentDialog` = 210 lines (adapter picker → create stub)
- `NewIssueDialog` = 1699 lines (rich form: assignees, projects, policies, mentions, dragdrop, advanced panel)

Other divergence indicators from imports:

- `NewIssueDialog` imports `agent-config-primitives` (`DraftInput`, `ChoosePathButton`, etc.), `ToggleSwitch`, `Popover`, large piece of `@dnd-kit`, markdown editors — i.e. a full inline form.
- `NewAgentDialog` imports `Dialog`, `Button`, adapter-registry helpers — a chooser, not a full form.
- `NewGoalDialog` and `NewProjectDialog` not examined in detail here; their size is likely between the two extremes.

## Open questions / risks

- Is `NewIssueDialog` intended to be "the" form and the others are just chooser-stubs that redirect to a detail page? That shape would be load-bearing. Currently unclear from static reading.
- Without a generic base, adding a fifth entity (e.g. `NewRoutineDialog`) means another copy of the dialog-open-context wiring. The `useDialog()` context already carries the per-entity open/close flags — it would be the natural integration point if consolidation is pursued.

## Pattern use in Stage 4 analysis

If Stage 4 were to name a composition "new-entity-dialog" for future reference, the canonical definition would be:

> A `<Dialog>` opened from the `useDialog()` context, closed via a per-entity handler, containing an entity-specific body that submits through the matching API and invalidates the matching `queryKeys` on success.

Not yet codified as code. Documented here only.
