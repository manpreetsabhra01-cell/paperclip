# Sidebar Chrome

Left-rail and settings-sidebar UI that wraps the main app surface.

**Instances: 6 "outer" + 2 sidebar menus = 8 components in the family.**
`Sidebar`, `InstanceSidebar`, `CompanySettingsSidebar`, `CompanyRail`, `access/CompanySettingsNav`, `MobileBottomNav` + `SidebarAccountMenu`, `SidebarCompanyMenu`.

> **Extraction-only pass.** Does not resolve the naming inconsistencies (Sidebar vs Rail vs Nav). See [components-review.md §Naming inconsistencies — Sidebar / Rail / Nav](../components/components-review.md#sidebar--rail--nav) and [§Likely duplicates #4 and #7](../components/components-review.md#4-sidebar-menu-pair-sidebaraccountmenu--sidebarcompanymenu).

## Instances

**Outer chrome (6):**

| Component | Lines | Uses | Role (inferred) |
|---|---|---|---|
| `Sidebar.tsx` | (unread) | ≥3 | Main app navigation sidebar |
| `InstanceSidebar.tsx` | (unread) | 1 | Instance-settings scope |
| `CompanySettingsSidebar.tsx` | (unread) | 2 | Company-settings scope |
| `CompanyRail.tsx` | 260 | 1 | Narrow vertical rail of sortable companies (dnd-kit) |
| `access/CompanySettingsNav.tsx` | (unread) | 1 | Tab-bar-style nav at the top of a settings surface |
| `MobileBottomNav.tsx` | (unread) | ≥3 | Mobile-bottom-tab alternative to the desktop sidebar |

**Sidebar menus (2):**

| Component | Uses | Role (inferred) |
|---|---|---|
| `SidebarAccountMenu.tsx` | 2 | Account dropdown anchored to the sidebar |
| `SidebarCompanyMenu.tsx` | 2 | Company dropdown anchored to the sidebar |

## Composition

Each of the six outer-chrome components solves a different surface problem (main nav vs settings nav vs rail vs bottom-bar), so the set is not simply a duplicate family. What they share is:

- They all attach to an app-level layout slot (`Layout.tsx`).
- They all render navigation items (`SidebarNavItem` is a shared primitive, 3 uses).
- Most compose `SidebarSection` as a grouping primitive.
- `Sidebar` + `InstanceSidebar` + `CompanySettingsSidebar` share the "aside" shape; `CompanyRail` is a narrower 3rd-dimension variant; `MobileBottomNav` is a horizontal mobile variant; `CompanySettingsNav` is a top-of-page tab bar and arguably belongs in a different family ("page tab nav") rather than "sidebar chrome."

## Token note — none of these consume `sidebar-*` tokens

From [tokens-review.md §3](../tokens/tokens-review.md#3-sidebar--tokens-are-dead): the 8 `sidebar-*` color tokens defined in `index.css` (`--sidebar`, `--sidebar-foreground`, `--sidebar-primary`, `--sidebar-primary-foreground`, `--sidebar-accent`, `--sidebar-accent-foreground`, `--sidebar-border`, `--sidebar-ring`) have **0 code usages**. All six chrome components consume the general semantic tokens (`background`, `accent`, `border`) directly.

Either the `sidebar-*` family should be adopted (to enable theming the sidebar independently of the main surface), or deleted (since nothing uses it).

## The sidebar-menu pair

`SidebarAccountMenu` and `SidebarCompanyMenu` are the dropdown menus triggered from the sidebar (account actions, company switching). Both used exactly twice. Per the duplicate directive, documented as a pair here, not auto-merged. If merged, the natural shape would be `<SidebarMenu kind="account" | "company">` or `<SidebarMenu>` with `children` slots.

## Vocabulary observation (not resolved)

The chrome family uses three different names for the same category of affordance:

- **Sidebar** (3 of the 6): the persistent rail
- **Rail** (1): `CompanyRail` — narrower / sortable
- **Nav** (2): `MobileBottomNav` + `CompanySettingsNav`

Whether these reflect real category distinctions or casual naming is a call for the founder. Pattern extraction records the observation and moves on.

## Related components and patterns

- `SidebarNavItem` — shared nav-item primitive (3 uses; no story — see [components-review.md §Story gaps](../components/components-review.md#story-coverage-gaps))
- `SidebarSection` — shared grouping primitive (1 use; below detail-file threshold)
- [entity-row.md](./entity-row.md) — tangential, since the item in a sidebar is closer to a nav-item than an entity row
