# IssueChatThread

`ui/src/components/IssueChatThread.tsx`

[INFER] Composite component. No file-level docstring.

## Quick facts

- **Category:** `composite`
- **Usage:** 4 imports (2 pages, 2 components)
- **Storybook:** yes — see ui/storybook/stories/ (thematic stories, not per-component)
- **File size:** 2399 lines

## Props

### `IssueChatComposerProps`

```ts
onImageUpload?: (file: File) => Promise<string>;
onAttachImage?: (file: File) => Promise<void>;
draftKey?: string;
enableReassign?: boolean;
reassignOptions?: InlineEntityOption[];
currentAssigneeValue?: string;
suggestedAssigneeValue?: string;
mentions?: MentionOption[];
agentMap?: Map<string, Agent>;
composerDisabledReason?: string | null;
issueStatus?: string;
```

### `IssueChatThreadProps`

```ts
comments: IssueChatComment[];
feedbackVotes?: FeedbackVote[];
feedbackDataSharingPreference?: FeedbackDataSharingPreference;
feedbackTermsUrl?: string | null;
linkedRuns?: IssueChatLinkedRun[];
timelineEvents?: IssueTimelineEvent[];
liveRuns?: LiveRunForIssue[];
activeRun?: ActiveRunForIssue | null;
blockedBy?: IssueRelationIssueSummary[];
companyId?: string | null;
projectId?: string | null;
issueStatus?: string;
agentMap?: Map<string, Agent>;
currentUserId?: string | null;
userLabelMap?: ReadonlyMap<string, string> | null;
userProfileMap?: ReadonlyMap<string, CompanyUserProfile> | null;
onVote?: (
commentId: string,
vote: FeedbackVoteValue,
options?: { allowSharing?: boolean; reason?: string
```

### `IssueChatErrorBoundaryProps`

```ts
resetKey: string;
messages: readonly ThreadMessage[];
emptyMessage: string;
variant: "full" | "embedded";
children: ReactNode;
```

## Composes

- **Primitives:** [avatar](./Avatar.md), [button](./Button.md), [dialog](./Dialog.md), `dropdown-menu`, [popover](./Popover.md), [textarea](./Textarea.md), [tooltip](./Tooltip.md)

## Used by

- **Pages:** `IssueChatUxLab`, `IssueDetail`
