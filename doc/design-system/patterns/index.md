# Patterns — Index

- **Generated:** 2026-04-21
- **Repo SHA:** a26e1288b627e82c554445732c7d844648e6b5e1
- **Scope:** `ui/` (@paperclipai/ui) — pages + components
- **Review:** [patterns-review.md](./patterns-review.md)

## What a pattern is

A *pattern* is a composition of components that recurs across pages or across composites — something that has a shape, not just a component name. Pattern documents describe the shape and list the current instances. They do not prescribe refactors.

Patterns were identified by:
1. Reading `_pages.json` and `_composition-graph.json` (Stage 2 scratch).
2. Looking for import-set intersections across pages or composition-graph neighborhoods.
3. Cross-referencing the duplicate families surfaced in [components-review.md §Likely duplicates](../components/components-review.md#likely-duplicates).
4. Checking the Paperclip-domain checklist from the extraction skill (heartbeat, run-transcript row, agent card, approval gate, cost display, metadata grid).

## Pattern inventory

Sorted by instance count. Patterns with ≥3 instances get their own detail doc; pairs below the threshold are included per directive but called out.

| Pattern | Instances | Doc |
|---|---|---|
| [List page](./list-page.md) | 12 | ✓ |
| [Detail page](./detail-page.md) | 8 | ✓ |
| [Sidebar chrome](./sidebar-chrome.md) | 6 outer + 2 menus | ✓ |
| [Finance / accounting card](./finance-card.md) | 5 | ✓ |
| [Entity properties panel](./entity-properties-panel.md) | 4 entity-specific + 1 generic chrome | ✓ |
| [Entity-creation dialog](./entity-creation-dialog.md) | 4 | ✓ |
| [Status display](./status-display.md) | 3 components + 1 catalog | ✓ |
| [Entity row](./entity-row.md) | 3 | ✓ |
| [Subscription panel](./subscription-panel.md) | 2 (below threshold — documented) | ✓ |
| [Quota display](./quota-display.md) | 2 (below threshold — documented) | ✓ |

See [patterns-review.md](./patterns-review.md) for:
- Pattern opportunities that don't yet meet the threshold but are domain-relevant (heartbeat, run-transcript row, agent card, approval gate, cost display, metric cell, severity indicator).
- Variance analysis across patterns.
- Which patterns are safe to codify and which are blocked on upstream token or naming decisions.

## Scope notes

- **Out of this pass:** deep pattern extraction from the UX Lab pages (`IssueChatUxLab`, `RunTranscriptUxLab`, `InviteUxLab`). Those are acknowledged prototypes — pattern work there should follow explicit founder direction, not auto-extraction.
- **Out of this pass:** the plugin SDK contract surface. Patterns emerge from the host `ui/`; if the SDK contract is fulfilled (see [components-review.md §Plugin SDK contract gap](../components/components-review.md#plugin-sdk-contract-gap)), those host implementations become additional pattern instances and this doc will need a re-run.
