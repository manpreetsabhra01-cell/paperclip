# Components — Index

- **Generated:** 2026-04-21
- **Repo SHA:** a26e1288b627e82c554445732c7d844648e6b5e1
- **Scope:** `ui/` + plugin SDK contracts
- **Review:** [components-review.md](./components-review.md)

## Counts

- **Total component files:** 135
- **With dedicated detail files (3+ code uses):** 53
- **Below threshold (1–2 uses):** 76
- **Storybook-only (in stories, 0 code uses):** 4
- **Dead (no uses, no stories):** 0
- **Non-component files (hooks, defaults):** 2
- **Plugin SDK contracts:** 11 (2 implemented by name, 9 contract-only — see §Plugin SDK contracts below)

### By category

| Category | Count |
|---|---|
| composite | 64 |
| primitive | 22 |
| standalone | 47 |
| utility-or-hook | 2 |

**Status markers in the tables below:**

- 📗 **documented** — ≥3 imports, has its own detail file in this directory
- 📘 **below-threshold** — 1–2 imports, no detail file
- 📙 **storybook-only** — 0 code imports, but appears in a story file
- ☠️ **dead** — 0 imports, 0 stories
- 🔌 **contract-only** — plugin SDK ambient declaration with no matching host implementation

---

## Primitives — `ui/src/components/ui/` (shadcn, 22)

All 22 shadcn primitives, by file name. These are the non-negotiable UI vocabulary — composites should consume these before reaching for custom markup.

| Component | Path | Uses | Pages / Comps | Story |
|---|---|---|---|---|
| 📗 [Button](./Button.md) | `ui/src/components/ui/button.tsx` | 81 | 41 / 38 | ✓ |
| 📗 [Popover](./Popover.md) | `ui/src/components/ui/popover.tsx` | 27 | 6 / 20 | ✓ |
| 📗 [Dialog](./Dialog.md) | `ui/src/components/ui/dialog.tsx` | 21 | 7 / 14 | ✓ |
| 📗 [Input](./Input.md) | `ui/src/components/ui/input.tsx` | 20 | 10 / 10 | ✓ |
| 📗 [Badge](./Badge.md) | `ui/src/components/ui/badge.tsx` | 18 | 11 / 7 | ✓ |
| 📗 [Card](./Card.md) | `ui/src/components/ui/card.tsx` | 18 | 10 / 8 | ✓ |
| 📗 [Tabs](./Tabs.md) | `ui/src/components/ui/tabs.tsx` | 15 | 13 / 2 | ✓ |
| 📗 [Separator](./Separator.md) | `ui/src/components/ui/separator.tsx` | 11 | 7 / 4 | ✓ |
| 📗 [Tooltip](./Tooltip.md) | `ui/src/components/ui/tooltip.tsx` | 11 | 3 / 7 | ✓ |
| 📗 [Select](./Select.md) | `ui/src/components/ui/select.tsx` | 10 | 5 / 5 | ✓ |
| 📗 [Textarea](./Textarea.md) | `ui/src/components/ui/textarea.tsx` | 9 | 4 / 5 | ✓ |
| 📗 [Collapsible](./Collapsible.md) | `ui/src/components/ui/collapsible.tsx` | 8 | 4 / 4 | — |
| 📗 [DropdownMenu](./DropdownMenu.md) | `ui/src/components/ui/dropdown-menu.tsx` | 8 | 3 / 5 | — |
| 📗 [Label](./Label.md) | `ui/src/components/ui/label.tsx` | 8 | 5 / 3 | ✓ |
| 📗 [ToggleSwitch](./ToggleSwitch.md) | `ui/src/components/ui/toggle-switch.tsx` | 8 | 5 / 3 | ✓ |
| 📗 [Avatar](./Avatar.md) | `ui/src/components/ui/avatar.tsx` | 7 | 3 / 4 | — |
| 📗 [Checkbox](./Checkbox.md) | `ui/src/components/ui/checkbox.tsx` | 6 | 4 / 2 | ✓ |
| 📗 [Skeleton](./Skeleton.md) | `ui/src/components/ui/skeleton.tsx` | 6 | 3 / 3 | — |
| 📗 [ScrollArea](./ScrollArea.md) | `ui/src/components/ui/scroll-area.tsx` | 3 | 2 / 1 | — |
| 📘 Breadcrumb | `ui/src/components/ui/breadcrumb.tsx` | 2 | 1 / 1 | — |
| 📘 Command | `ui/src/components/ui/command.tsx` | 2 | 1 / 1 | ✓ |
| 📘 Sheet | `ui/src/components/ui/sheet.tsx` | 2 | 2 / 0 | — |

---

## Composites (64)

Components that import 1+ other component from `@/components/*`. Application-level feature UI.

| Component | Path | Uses | Pages / Comps | Story |
|---|---|---|---|---|
| 📗 [PageSkeleton](./PageSkeleton.md) | `ui/src/components/PageSkeleton.tsx` | 23 | 22 / 1 | ✓ |
| 📗 [EmptyState](./EmptyState.md) | `ui/src/components/EmptyState.tsx` | 20 | 19 / 1 | ✓ |
| 📗 [agent-config-primitives](./agent-config-primitives.md) | `ui/src/components/agent-config-primitives.tsx` | 19 | 4 / 3 | ✓ |
| 📗 [Identity](./Identity.md) | `ui/src/components/Identity.tsx` | 19 | 7 / 12 | ✓ |
| 📗 [StatusIcon](./StatusIcon.md) | `ui/src/components/StatusIcon.tsx` | 14 | 5 / 9 | — |
| 📗 [AgentIconPicker](./AgentIconPicker.md) | `ui/src/components/AgentIconPicker.tsx` | 13 | 4 / 9 | ✓ |
| 📗 [PathInstructionsModal](./PathInstructionsModal.md) | `ui/src/components/PathInstructionsModal.tsx` | 12 | 2 / 3 | ✓ |
| 📗 [PageTabBar](./PageTabBar.md) | `ui/src/components/PageTabBar.tsx` | 10 | 9 / 1 | ✓ |
| 📗 [InlineEntitySelector](./InlineEntitySelector.md) | `ui/src/components/InlineEntitySelector.tsx` | 8 | 2 / 4 | ✓ |
| 📗 [IssuesList](./IssuesList.md) | `ui/src/components/IssuesList.tsx` | 6 | 5 / 1 | ✓ |
| 📗 [AgentConfigForm](./AgentConfigForm.md) | `ui/src/components/AgentConfigForm.tsx` | 5 | 3 / 0 | ✓ |
| 📗 [PriorityIcon](./PriorityIcon.md) | `ui/src/components/PriorityIcon.tsx` | 5 | 2 / 3 | ✓ |
| 📗 [IssueChatThread](./IssueChatThread.md) | `ui/src/components/IssueChatThread.tsx` | 4 | 2 / 2 | ✓ |
| 📗 [ApprovalCard](./ApprovalCard.md) | `ui/src/components/ApprovalCard.tsx` | 3 | 2 / 1 | ✓ |
| 📗 [BudgetPolicyCard](./BudgetPolicyCard.md) | `ui/src/components/BudgetPolicyCard.tsx` | 3 | 3 / 0 | ✓ |
| 📗 [IssueFiltersPopover](./IssueFiltersPopover.md) | `ui/src/components/IssueFiltersPopover.tsx` | 3 | 1 / 2 | ✓ |
| 📗 [IssueLinkQuicklook](./IssueLinkQuicklook.md) | `ui/src/components/IssueLinkQuicklook.tsx` | 3 | 0 / 2 | ✓ |
| 📗 [IssueWorkspaceCard](./IssueWorkspaceCard.md) | `ui/src/components/IssueWorkspaceCard.tsx` | 3 | 1 / 2 | ✓ |
| 📗 [RoutineRunVariablesDialog](./RoutineRunVariablesDialog.md) | `ui/src/components/RoutineRunVariablesDialog.tsx` | 3 | 2 / 1 | ✓ |
| 📗 [WorkspaceRuntimeControls](./WorkspaceRuntimeControls.md) | `ui/src/components/WorkspaceRuntimeControls.tsx` | 3 | 2 / 1 | ✓ |
| 📘 AgentActionButtons | `ui/src/components/AgentActionButtons.tsx` | 2 | 2 / 0 | ✓ |
| 📘 CommandPalette | `ui/src/components/CommandPalette.tsx` | 2 | 0 / 2 | ✓ |
| 📘 IssueColumns | `ui/src/components/IssueColumns.tsx` | 2 | 1 / 1 | ✓ |
| 📘 IssueContinuationHandoff | `ui/src/components/IssueContinuationHandoff.tsx` | 2 | 1 / 1 | ✓ |
| 📘 IssueDocumentsSection | `ui/src/components/IssueDocumentsSection.tsx` | 2 | 1 / 1 | ✓ |
| 📘 IssueProperties | `ui/src/components/IssueProperties.tsx` | 2 | 1 / 1 | ✓ |
| 📘 NewIssueDialog | `ui/src/components/NewIssueDialog.tsx` | 2 | 0 / 2 | ✓ |
| 📘 OutputFeedbackButtons | `ui/src/components/OutputFeedbackButtons.tsx` | 2 | 0 / 2 | — |
| 📘 ProjectWorkspaceSummaryCard | `ui/src/components/ProjectWorkspaceSummaryCard.tsx` | 2 | 0 / 2 | ✓ |
| 📘 ReportsToPicker | `ui/src/components/ReportsToPicker.tsx` | 2 | 1 / 1 | ✓ |
| 📘 RoutineVariablesEditor | `ui/src/components/RoutineVariablesEditor.tsx` | 2 | 2 / 0 | ✓ |
| 📘 Sidebar | `ui/src/components/Sidebar.tsx` | 2 | 0 / 2 | ✓ |
| 📘 SidebarAccountMenu | `ui/src/components/SidebarAccountMenu.tsx` | 2 | 0 / 2 | ✓ |
| 📘 SidebarCompanyMenu | `ui/src/components/SidebarCompanyMenu.tsx` | 2 | 0 / 2 | ✓ |
| 📘 CompanySettingsNav | `ui/src/components/access/CompanySettingsNav.tsx` | 1 | 0 / 1 | — |
| 📘 ModeBadge | `ui/src/components/access/ModeBadge.tsx` | 1 | 1 / 0 | — |
| 📘 BillerSpendCard | `ui/src/components/BillerSpendCard.tsx` | 1 | 1 / 0 | ✓ |
| 📘 BreadcrumbBar | `ui/src/components/BreadcrumbBar.tsx` | 1 | 0 / 1 | ✓ |
| 📘 BudgetIncidentCard | `ui/src/components/BudgetIncidentCard.tsx` | 1 | 1 / 0 | ✓ |
| 📘 CommentThread | `ui/src/components/CommentThread.tsx` | 1 | 0 / 1 | ✓ |
| 📘 CompanyRail | `ui/src/components/CompanyRail.tsx` | 1 | 0 / 1 | ✓ |
| 📘 DocumentDiffModal | `ui/src/components/DocumentDiffModal.tsx` | 1 | 0 / 1 | ✓ |
| 📘 FilterBar | `ui/src/components/FilterBar.tsx` | 1 | 1 / 0 | ✓ |
| 📘 FinanceBillerCard | `ui/src/components/FinanceBillerCard.tsx` | 1 | 1 / 0 | ✓ |
| 📘 FinanceKindCard | `ui/src/components/FinanceKindCard.tsx` | 1 | 1 / 0 | ✓ |
| 📘 FinanceTimelineCard | `ui/src/components/FinanceTimelineCard.tsx` | 1 | 1 / 0 | ✓ |
| 📘 GoalProperties | `ui/src/components/GoalProperties.tsx` | 1 | 1 / 0 | ✓ |
| 📘 IssuesQuicklook | `ui/src/components/IssuesQuicklook.tsx` | 1 | 0 / 1 | ✓ |
| 📘 JsonSchemaForm | `ui/src/components/JsonSchemaForm.tsx` | 1 | 1 / 0 | ✓ |
| 📘 KeyboardShortcutsCheatsheet | `ui/src/components/KeyboardShortcutsCheatsheet.tsx` | 1 | 0 / 1 | ✓ |
| 📘 NewAgentDialog | `ui/src/components/NewAgentDialog.tsx` | 1 | 0 / 1 | ✓ |
| 📘 NewGoalDialog | `ui/src/components/NewGoalDialog.tsx` | 1 | 0 / 1 | ✓ |
| 📘 NewProjectDialog | `ui/src/components/NewProjectDialog.tsx` | 1 | 0 / 1 | ✓ |
| 📘 OnboardingWizard | `ui/src/components/OnboardingWizard.tsx` | 1 | 0 / 0 | ✓ |
| 📘 ProjectProperties | `ui/src/components/ProjectProperties.tsx` | 1 | 1 / 0 | ✓ |
| 📘 PropertiesPanel | `ui/src/components/PropertiesPanel.tsx` | 1 | 0 / 1 | — |
| 📘 ProviderQuotaCard | `ui/src/components/ProviderQuotaCard.tsx` | 1 | 1 / 0 | ✓ |
| 📘 ScheduleEditor | `ui/src/components/ScheduleEditor.tsx` | 1 | 1 / 0 | ✓ |
| 📘 SidebarAgents | `ui/src/components/SidebarAgents.tsx` | 1 | 0 / 1 | — |
| 📘 SidebarProjects | `ui/src/components/SidebarProjects.tsx` | 1 | 0 / 1 | — |
| 📙 AccountingModelCard | `ui/src/components/AccountingModelCard.tsx` | 0 | 0 / 0 | ✓ |
| 📙 AgentProperties | `ui/src/components/AgentProperties.tsx` | 0 | 0 / 0 | ✓ |
| 📙 CompanySwitcher | `ui/src/components/CompanySwitcher.tsx` | 0 | 0 / 0 | ✓ |
| 📙 ExecutionParticipantPicker | `ui/src/components/ExecutionParticipantPicker.tsx` | 0 | 0 / 0 | ✓ |

---

## Standalones (47)

Components that import no other `@/components/*`. Usually: icons, self-contained widgets, components that only depend on radix / lucide / local libs. The fact that they import zero composites or primitives is itself a data point — some of these probably should be using primitives.

| Component | Path | Uses | Pages / Comps | Story |
|---|---|---|---|---|
| 📗 [StatusBadge](./StatusBadge.md) | `ui/src/components/StatusBadge.tsx` | 19 | 12 / 7 | ✓ |
| 📗 [MarkdownEditor](./MarkdownEditor.md) | `ui/src/components/MarkdownEditor.tsx` | 16 | 5 / 9 | ✓ |
| 📗 [MarkdownBody](./MarkdownBody.md) | `ui/src/components/MarkdownBody.tsx` | 11 | 5 / 6 | ✓ |
| 📗 [EntityRow](./EntityRow.md) | `ui/src/components/EntityRow.tsx` | 6 | 6 / 0 | ✓ |
| 📗 [InlineEditor](./InlineEditor.md) | `ui/src/components/InlineEditor.tsx` | 6 | 4 / 2 | ✓ |
| 📗 [ApprovalPayload](./ApprovalPayload.md) | `ui/src/components/ApprovalPayload.tsx` | 4 | 2 / 2 | — |
| 📗 [CompanyPatternIcon](./CompanyPatternIcon.md) | `ui/src/components/CompanyPatternIcon.tsx` | 4 | 3 / 1 | ✓ |
| 📗 [IssueReferencePill](./IssueReferencePill.md) | `ui/src/components/IssueReferencePill.tsx` | 4 | 1 / 3 | — |
| 📗 [ActivityCharts](./ActivityCharts.md) | `ui/src/components/ActivityCharts.tsx` | 3 | 2 / 1 | ✓ |
| 📗 [CopyText](./CopyText.md) | `ui/src/components/CopyText.tsx` | 3 | 2 / 1 | ✓ |
| 📗 [IssueRow](./IssueRow.md) | `ui/src/components/IssueRow.tsx` | 3 | 1 / 2 | ✓ |
| 📗 [PackageFileTree](./PackageFileTree.md) | `ui/src/components/PackageFileTree.tsx` | 3 | 3 / 0 | ✓ |
| 📗 [SidebarNavItem](./SidebarNavItem.md) | `ui/src/components/SidebarNavItem.tsx` | 3 | 0 / 3 | — |
| 📗 [RunTranscriptView](./RunTranscriptView.md) | `ui/src/components/transcript/RunTranscriptView.tsx` | 3 | 2 / 1 | — |
| 📘 ActivityRow | `ui/src/components/ActivityRow.tsx` | 2 | 2 / 0 | ✓ |
| 📘 AsciiArtAnimation | `ui/src/components/AsciiArtAnimation.tsx` | 2 | 1 / 1 | ✓ |
| 📘 BudgetSidebarMarker | `ui/src/components/BudgetSidebarMarker.tsx` | 2 | 0 / 2 | ✓ |
| 📘 CloudAccessGate | `ui/src/components/CloudAccessGate.tsx` | 2 | 0 / 0 | — |
| 📘 CompanySettingsSidebar | `ui/src/components/CompanySettingsSidebar.tsx` | 2 | 0 / 2 | — |
| 📘 EnvVarEditor | `ui/src/components/EnvVarEditor.tsx` | 2 | 0 / 2 | ✓ |
| 📘 ExecutionWorkspaceCloseDialog | `ui/src/components/ExecutionWorkspaceCloseDialog.tsx` | 2 | 1 / 1 | ✓ |
| 📘 GoalTree | `ui/src/components/GoalTree.tsx` | 2 | 2 / 0 | ✓ |
| 📘 IssueGroupHeader | `ui/src/components/IssueGroupHeader.tsx` | 2 | 1 / 1 | ✓ |
| 📘 IssueReferenceActivitySummary | `ui/src/components/IssueReferenceActivitySummary.tsx` | 2 | 1 / 1 | — |
| 📘 IssueRelatedWorkPanel | `ui/src/components/IssueRelatedWorkPanel.tsx` | 2 | 1 / 1 | — |
| 📘 IssueRunLedger | `ui/src/components/IssueRunLedger.tsx` | 2 | 1 / 1 | ✓ |
| 📘 Layout | `ui/src/components/Layout.tsx` | 2 | 0 / 1 | — |
| 📘 MetricCard | `ui/src/components/MetricCard.tsx` | 2 | 2 / 0 | ✓ |
| 📘 OpenCodeLogoIcon | `ui/src/components/OpenCodeLogoIcon.tsx` | 2 | 0 / 1 | — |
| 📘 ProjectWorkspacesContent | `ui/src/components/ProjectWorkspacesContent.tsx` | 2 | 2 / 0 | ✓ |
| 📘 QuotaBar | `ui/src/components/QuotaBar.tsx` | 2 | 0 / 2 | ✓ |
| 📘 RunChatSurface | `ui/src/components/RunChatSurface.tsx` | 2 | 0 / 2 | ✓ |
| 📘 ScrollToBottom | `ui/src/components/ScrollToBottom.tsx` | 2 | 2 / 0 | — |
| 📘 SwipeToArchive | `ui/src/components/SwipeToArchive.tsx` | 2 | 1 / 1 | ✓ |
| 📘 ActiveAgentsPanel | `ui/src/components/ActiveAgentsPanel.tsx` | 1 | 1 / 0 | ✓ |
| 📘 ClaudeSubscriptionPanel | `ui/src/components/ClaudeSubscriptionPanel.tsx` | 1 | 0 / 1 | ✓ |
| 📘 CodexSubscriptionPanel | `ui/src/components/CodexSubscriptionPanel.tsx` | 1 | 0 / 1 | ✓ |
| 📘 DevRestartBanner | `ui/src/components/DevRestartBanner.tsx` | 1 | 0 / 1 | — |
| 📘 HermesIcon | `ui/src/components/HermesIcon.tsx` | 1 | 0 / 0 | — |
| 📘 ImageGalleryModal | `ui/src/components/ImageGalleryModal.tsx` | 1 | 1 / 0 | ✓ |
| 📘 InstanceSidebar | `ui/src/components/InstanceSidebar.tsx` | 1 | 0 / 1 | — |
| 📘 KanbanBoard | `ui/src/components/KanbanBoard.tsx` | 1 | 0 / 1 | ✓ |
| 📘 LiveRunWidget | `ui/src/components/LiveRunWidget.tsx` | 1 | 1 / 0 | ✓ |
| 📘 MobileBottomNav | `ui/src/components/MobileBottomNav.tsx` | 1 | 0 / 1 | ✓ |
| 📘 SidebarSection | `ui/src/components/SidebarSection.tsx` | 1 | 0 / 1 | — |
| 📘 ToastViewport | `ui/src/components/ToastViewport.tsx` | 1 | 0 / 1 | — |
| 📘 WorktreeBanner | `ui/src/components/WorktreeBanner.tsx` | 1 | 0 / 1 | ✓ |

---

## Non-component files (2)

These live in `ui/src/components/` by convention but don't export React components.

| File | Path | Role |
|---|---|---|
| `agent-config-defaults` | `ui/src/components/agent-config-defaults.ts` | module with shared constants/defaults |
| `useLiveRunTranscripts` | `ui/src/components/transcript/useLiveRunTranscripts.ts` | React hook |

---

## Plugin SDK contracts (11)

Ambient component declarations from [`packages/plugins/sdk/src/ui/components.ts`](../../../packages/plugins/sdk/src/ui/components.ts). These are types-only; the host provides implementations at runtime via `renderSdkUiComponent(name, props)`.

> **Hybrid status is intentional (2026-04-21 decision).** Two components are implemented by the host. The other nine are **contract-only** — the types exist so plugin authors can code against them, but rendering today will fail at runtime. The 9 contract-only components carry a `@status contract-only` JSDoc tag in the SDK source, which appears in IDE tooltips at call sites. Prioritization of which to implement first is a separate plugin-SDK roadmap conversation, not a DS decision. See [components-review.md §Plugin SDK hybrid status](./components-review.md#plugin-sdk-hybrid-status-prioritization-deferred).

| SDK Component | Implementation | Status |
|---|---|---|
| `MetricCard` | [`ui/src/components/MetricCard.tsx`](../../../ui/src/components/MetricCard.tsx) | 📗 **implemented** |
| `StatusBadge` | [`ui/src/components/StatusBadge.tsx`](../../../ui/src/components/StatusBadge.tsx) | 📗 **implemented** |
| `DataTable` | — | 🔌 **contract-only** |
| `TimeseriesChart` | — | 🔌 **contract-only** (distinct from `ActivityCharts.tsx`, which has a different API) |
| `MarkdownBlock` | — | 🔌 **contract-only** (`MarkdownBody.tsx` is the host's markdown renderer, name differs) |
| `KeyValueList` | — | 🔌 **contract-only** |
| `ActionBar` | — | 🔌 **contract-only** (`AgentActionButtons.tsx` is role-specific, not a match) |
| `LogView` | — | 🔌 **contract-only** |
| `JsonTree` | — | 🔌 **contract-only** |
| `Spinner` | — | 🔌 **contract-only** |
| `ErrorBoundary` | — | 🔌 **contract-only** |

All 9 contract-only entries carry `@status contract-only` in their JSDoc block (see [`packages/plugins/sdk/src/ui/components.ts`](../../../packages/plugins/sdk/src/ui/components.ts) lines 253–316).
