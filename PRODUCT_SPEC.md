# Data Grid — Product Specification

## 1. Product Vision

Build a commercial-grade, framework-agnostic Data Grid engine with first-class
adapters for modern frontend frameworks.

The product should solve one of the most painful problems in frontend
development:

> Building a serious data table from scratch.

The goal is not to create another basic table component.

The goal is to create a reusable **Data Grid engine** that provides the
difficult functionality once, while allowing different frontend frameworks to
consume the same underlying engine.

The core product should be framework-independent.

Initial framework:

- React

Future adapters may include:

- Vue
- Svelte
- Solid
- Angular

The architecture must make those future adapters possible without rewriting the
core.

---

# 2. Product Philosophy

The product should follow these principles:

1. **Framework agnostic**
2. **Type safe**
3. **Fast**
4. **Accessible**
5. **Highly customizable**
6. **Simple by default**
7. **Powerful when needed**
8. **Minimal dependencies**
9. **Excellent developer experience**
10. **No unnecessary abstractions**

The API should make simple tables extremely easy while allowing advanced
applications to progressively opt into more functionality.

---

# 3. Target Users

Primary users:

- Frontend developers
- Full-stack developers
- SaaS developers
- Internal tools developers
- Enterprise application developers
- Teams building dashboards
- Teams replacing custom table implementations

Typical problem:

> "We need a table with sorting, filtering, pagination, editing, selection,
> virtualization, column management, etc., and now our table component has
> become 5,000 lines of code."

The product should eliminate that problem.

---

# 4. Competitive Positioning

The product should occupy the space between:

### Basic table libraries

Easy to use but lacking advanced functionality.

### Headless table libraries

Powerful but require substantial UI and interaction implementation.

### Enterprise data grids

Extremely capable but often complex, expensive, or difficult to customize.

The product should aim for:

> **The power of an enterprise data grid with the developer experience of a
> lightweight library.**

Do not make unsupported claims about competitors.

Competitor research should be performed separately before making commercial or
feature decisions.

---

# 5. Technology Strategy

## Core

Use:

- TypeScript
- Strict TypeScript
- ESM
- Framework-independent APIs

The core must not import:

- React
- Vue
- Svelte
- Angular
- Browser-specific UI frameworks

The core should ideally be usable in any JavaScript/TypeScript environment
capable of executing the package.

---

# 6. Framework Architecture

The architecture should follow:

```text
                @portentgrid/core
                     │
      ┌──────────────┼──────────────┐
      │              │              │
@portentgrid/react  @portentgrid/vue  @portentgrid/svelte
      │              │              │
      ▼              ▼              ▼
  React UI        Vue UI        Svelte UI
```

Only the React adapter is required initially.

Do NOT implement multiple framework adapters during the MVP.

The architecture must make future adapters possible without requiring changes to
the core API.

---

# 7. Package Structure

Start with:

```text
packages/
  core/
  react/

apps/
  demo/
  docs/
```

Future structure may become:

```text
packages/
  core/
  react/
  vue/
  svelte/
  angular/
```

Do not create packages merely for organizational purposes.

Keep the initial architecture as small as possible.

---

# 8. Core Responsibilities

The core should own grid behavior and state.

Potential responsibilities:

- Data management
- Column definitions
- Sorting
- Filtering
- Pagination
- Selection
- Column visibility
- Column order
- Column sizing
- Column pinning
- Row expansion
- Editing state
- Keyboard/navigation state
- Grid events
- Virtualization state
- Derived row/cell state

The core should NOT own:

- JSX
- HTML
- CSS
- React components
- Vue components
- DOM-specific UI
- framework-specific state management

---

# 9. Core API Philosophy

The core should expose a framework-neutral API.

Example:

```ts
const grid = createGrid({
  data,
  columns,
  sorting: true,
  filtering: true,
  pagination: true,
});
```

The exact API may change during architecture design.

The core should expose:

- State
- State transitions
- Derived state
- Commands/actions
- Events
- Configuration
- Public types

Framework adapters should translate those concepts into framework-native APIs.

---

# 10. React Adapter

The React adapter should provide a developer-friendly React API.

Example:

```tsx
import { DataGrid } from "@portentgrid/react";

<DataGrid
  data={users}
  columns={columns}
/>;
```

Advanced usage:

```tsx
<DataGrid
  data={users}
  columns={columns}
  sortable
  filterable
  searchable
  selectable
  pagination
/>;
```

The React adapter should feel like a normal React component.

Consumers should not need to understand the internal core architecture.

---

# 11. Data Model

The grid must support strongly typed generic row data.

Example:

```ts
type User = {
  id: string;
  name: string;
  email: string;
  role: string;
  status: "active" | "inactive";
};
```

Columns:

```ts
const columns: ColumnDef<User>[] = [
  {
    id: "name",
    header: "Name",
    accessorKey: "name",
  },
  {
    id: "email",
    header: "Email",
    accessorKey: "email",
  },
  {
    id: "status",
    header: "Status",
    accessorKey: "status",
  },
];
```

The type system should preserve the relationship between:

```text
Row type
    ↓
Column definition
    ↓
Cell value
    ↓
Cell renderer
```

Avoid `any` wherever possible.

---

# 12. Rendering Model

The core must not dictate how a framework renders the grid.

Instead, it should expose enough information for an adapter to render:

```text
Grid
 ├── Columns
 ├── Headers
 ├── Rows
 │    └── Cells
 └── Footer
```

The adapter determines how these become:

- DOM elements
- React components
- Vue components
- Svelte components
- etc.

---

# 13. Sorting

Support:

- Single-column sorting
- Multi-column sorting
- Ascending
- Descending
- Unsorted
- Custom sorting functions
- Controlled sorting
- Uncontrolled sorting
- Client-side sorting
- Server-side sorting

Example:

```tsx
<DataGrid
  data={users}
  columns={columns}
  sortable
/>;
```

The sorting engine belongs in the core.

The sorting UI belongs in the adapter/UI layer.

---

# 14. Filtering

Support:

- Global filtering
- Column filtering
- Text filters
- Number filters
- Boolean filters
- Select filters
- Date filters
- Custom filters
- Custom filter functions
- Client-side filtering
- Server-side filtering

The core should provide filtering logic and state.

The UI should remain customizable.

Do not hardcode every filter UI into the core.

---

# 15. Pagination

Support:

### Client-side

The grid manages:

- Page
- Page size
- Visible rows

### Server-side

The consumer manages:

- Data fetching
- Total row count
- Page changes
- Page size changes

Example:

```tsx
<DataGrid
  data={users}
  columns={columns}
  pagination={{
    mode: "server",
    page: 1,
    pageSize: 25,
    totalRows: 12450,
    onChange: handlePaginationChange,
  }}
/>;
```

Do not couple the core to:

- fetch
- Axios
- TanStack Query
- any backend
- any API format

---

# 16. Row Selection

Support:

- Single selection
- Multi-selection
- Select all
- Indeterminate state
- Controlled selection
- Uncontrolled selection
- Selection events

Example:

```tsx
<DataGrid
  data={users}
  columns={columns}
  selectable
  onSelectionChange={setSelection}
/>;
```

Selection state belongs in the core.

Selection UI belongs to the adapter.

---

# 17. Column Management

Support:

- Column resizing
- Column reordering
- Column visibility
- Column pinning
- Minimum width
- Maximum width
- Fixed width
- Flexible width
- Persistable column configuration

Example:

```tsx
<DataGrid
  data={users}
  columns={columns}
  columnResizing
  columnReordering
  columnVisibility
/>;
```

Column state must be serializable where practical so applications can persist
user preferences.

---

# 18. Row Interaction

Support:

- Row click
- Double click
- Keyboard interaction
- Context menu hooks
- Expandable rows
- Custom row behavior

Example:

```tsx
<DataGrid
  data={users}
  columns={columns}
  onRowClick={(row) => openUser(row)}
/>;
```

The core should expose row interaction state/events without assuming what the
application does with them.

---

# 19. Inline Editing

Design an extensible editing system.

Support:

- Text editing
- Number editing
- Boolean editing
- Select editing
- Custom editors
- Validation
- Async save
- Cancel
- Commit
- Cell editing
- Row editing

Example:

```tsx
<DataGrid
  data={users}
  columns={columns}
  editable
  onCellEdit={handleEdit}
/>;
```

Do not couple editing to:

- React Hook Form
- Formik
- any validation library

External form libraries may be supported through integration examples.

---

# 20. Virtualization

Virtualization is a core performance requirement.

The grid should support large datasets.

Initial performance targets:

- 1,000 rows
- 10,000 rows
- 100,000 rows

The architecture should make it possible to support larger datasets later.

Virtualization must work correctly with:

- Selection
- Editing
- Keyboard navigation
- Expansion
- Sticky headers
- Column pinning

Do not implement virtualization prematurely if the architecture is not ready.

Benchmark before optimizing.

---

# 21. Accessibility

Accessibility is mandatory.

Support:

- Correct semantic structure
- Appropriate ARIA roles
- Keyboard navigation
- Focus management
- Screen reader labels
- Accessible sorting
- Accessible selection
- Accessible editing
- Visible focus indicators

Follow current WAI-ARIA guidance.

Accessibility must be part of the architecture rather than added at the end.

---

# 22. Styling Philosophy

The core has no styling.

The React adapter should provide sensible default styling.

However, consumers must be able to completely customize the appearance.

Support:

- CSS variables
- Custom class names
- Custom components
- Render props where appropriate
- Theme tokens
- Dark mode

Do NOT require Tailwind CSS for consumers.

Tailwind may be used for:

- demo
- documentation
- development tooling

but the published grid must work without Tailwind.

---

# 23. Headless Capability

The architecture should support a headless usage model.

Consumers should eventually be able to use:

```ts
const grid = createGrid(...)
```

and render their own UI.

The core should therefore expose enough information to build completely custom
interfaces.

This is important for:

- Design systems
- Enterprise applications
- Custom themes
- Framework adapters
- Accessibility customization

---

# 24. UI Components

The default React adapter may provide components such as:

```text
DataGrid
DataGridHeader
DataGridBody
DataGridRow
DataGridCell
DataGridToolbar
DataGridPagination
DataGridFilters
DataGridColumnMenu
DataGridEmptyState
```

These are implementation details and may change.

Do not create components solely because the names appear in this document.

Use the simplest architecture that preserves customization.

---

# 25. Loading / Empty / Error States

Provide customizable states:

```tsx
<DataGrid
  loading
  emptyState={<EmptyState />}
  errorState={<ErrorState />}
/>;
```

Consumers must be able to completely replace the default UI.

---

# 26. Server-Side Architecture

Server-side mode must be framework and networking agnostic.

The grid should expose state such as:

```ts
{
  sorting, filters, pagination;
}
```

and allow the application to react to changes.

Example conceptual flow:

```text
User interaction
       ↓
Grid state changes
       ↓
Application receives state
       ↓
Application fetches data
       ↓
Application provides new data
       ↓
Grid renders
```

The grid must not perform network requests itself.

---

# 27. State Architecture

Separate:

### Core state

- Sorting
- Filtering
- Pagination
- Selection
- Column visibility
- Column order
- Column sizing
- Column pinning
- Expansion
- Editing

from:

### UI state

- Menus
- Dialogs
- Toolbars
- Dropdowns
- Filter panels
- Visual loading indicators

The core should contain behavior, not visual implementation.

---

# 28. External State

The grid should support both:

### Uncontrolled

```tsx
<DataGrid
  data={users}
  columns={columns}
/>;
```

and:

### Controlled

```tsx
<DataGrid
  data={users}
  columns={columns}
  sorting={sorting}
  onSortingChange={setSorting}
/>;
```

Do not force users to adopt a specific state management library.

---

# 29. TanStack Integration

Investigate whether TanStack Table and/or TanStack Virtual can be used as
internal dependencies or architectural inspiration.

Do not automatically reinvent functionality that is already mature and well
tested.

Before adopting a dependency, evaluate:

- License
- Bundle size
- API compatibility
- Performance
- Extensibility
- Maintenance
- Ability to support framework-independent core
- Ability to build the desired commercial product on top

The final architecture must be justified.

Do not add dependencies simply because they are popular.

---

# 30. Dependency Philosophy

Keep the core dependency footprint extremely small.

Prefer:

```text
@portentgrid/core
    ↓
minimal dependencies
```

Avoid creating a dependency chain that makes the product difficult to maintain.

Framework-specific dependencies belong in framework-specific packages.

---

# 31. Performance Architecture

Performance must be measured rather than assumed.

Create benchmarks for:

- Initial render
- Sorting
- Filtering
- Selection
- Editing
- Column changes
- Scrolling
- Virtualization

Measure:

- CPU time
- Memory usage
- Render count
- Bundle size

Avoid premature optimization.

---

# 32. Testing

Use automated tests.

Core tests should be framework-independent whenever possible.

Test:

- Column definitions
- Sorting
- Filtering
- Pagination
- Selection
- Column visibility
- Column order
- Column sizing
- Column pinning
- Editing
- Expansion
- Controlled state
- Server state
- Keyboard behavior
- Virtualization logic

React adapter tests should verify:

- Rendering
- Interaction
- Accessibility
- Controlled/uncontrolled behavior
- Integration with the core

---

# 33. Package Build

Packages should support:

- ESM
- TypeScript declarations
- Tree-shaking
- Source maps
- npm publishing

Public exports must be explicit.

Avoid leaking internal modules as public APIs.

---

# 34. Demo Application

Create a realistic demo application.

The demo should include:

## Users

- Name
- Email
- Role
- Status
- Created date

## Tickets

- ID
- Title
- Status
- Priority
- Assignee
- Created date

## Products

- SKU
- Name
- Category
- Price
- Stock
- Revenue

The demo should demonstrate realistic combinations of features.

Avoid demos that only contain:

```text
Name | Age | Email
```

The product needs to show why a Data Grid exists.

---

# 35. Documentation

Create documentation focused on developer success.

Required sections:

- Installation
- Quick start
- Basic grid
- Columns
- Sorting
- Filtering
- Pagination
- Server-side data
- Selection
- Editing
- Column management
- Virtualization
- Accessibility
- Customization
- Headless usage
- TypeScript
- Performance
- Framework adapters

Every major feature should have a working example.

---

# 36. Example Developer Experience

A simple table should be possible with:

```tsx
import { DataGrid } from "@portentgrid/react";

<DataGrid
  data={users}
  columns={columns}
/>;
```

Adding functionality should remain declarative:

```tsx
<DataGrid
  data={users}
  columns={columns}
  sortable
  searchable
  filterable
  selectable
  pagination
/>;
```

Advanced consumers should have access to lower-level APIs.

---

# 37. Commercial Strategy

The initial implementation should NOT contain artificial feature restrictions.

The goal is to validate:

- Developer interest
- Adoption
- Performance
- API quality
- Real-world usage

Potential future commercial features may include:

- Advanced grouping
- Aggregation
- Pivot tables
- Excel export
- Advanced filtering builder
- Master/detail views
- Advanced enterprise integrations
- Priority support
- Commercial licenses

Do not implement licensing infrastructure during the MVP.

Do not artificially cripple the open-source core.

---

# 38. Open Source Strategy

The core should be architected so it can potentially be open source.

Possible future structure:

```text
@portentgrid/core
@portentgrid/react
@portentgrid/vue
@portentgrid/svelte
```

Commercial packages may eventually build on top of the open-source foundation.

Do not make final licensing decisions during initial implementation unless
explicitly requested.

---

# 39. Future Framework Adapters

Future adapters should consume the same core:

```text
            @portentgrid/core
                  │
  ┌───────────────┼────────────────┐
  │               │                │
React            Vue             Svelte
  │               │                │
  ▼               ▼                ▼
 UI              UI               UI
```

The core must remain framework-independent.

Do not add framework-specific concepts to core APIs.

---

# 40. Future Extensibility

The architecture should allow future capabilities such as:

- Grouping
- Aggregation
- Pivot tables
- Tree data
- Master/detail
- Charts
- Advanced export
- Excel integration
- Server-side data adapters
- Custom renderers
- Plugin system

Do not implement these during MVP.

Do not create abstractions solely to support hypothetical features.

---

# 41. Public API Stability

Treat exported types and functions as public contracts.

Avoid exposing:

- Internal state structures
- Private implementation details
- Temporary helpers

Use explicit public exports.

Breaking API changes should be intentional and documented.

---

# 42. Error Handling

Errors should be:

- Predictable
- Developer-friendly
- Actionable

Avoid silent failures.

Do not throw obscure internal errors when a configuration problem can be
detected and explained clearly.

---

# 43. Developer Experience Requirements

A developer should be able to:

1. Install the package.
2. Import the DataGrid.
3. Define typed columns.
4. Render data.
5. Add sorting.
6. Add filtering.
7. Add pagination.
8. Add selection.
9. Customize cells.
10. Customize styling.

without reading the source code.

Advanced functionality should be discoverable through documentation.

---

# 44. MVP Scope

The MVP should include:

### Core

- Generic typed data
- Column definitions
- Row/cell model
- Sorting
- Filtering
- Pagination
- Selection
- Column visibility
- Column ordering
- Column sizing
- Basic expansion
- Controlled/uncontrolled state

### React

- DataGrid component
- Custom cell rendering
- Custom header rendering
- Custom states
- Default UI
- Accessibility
- Keyboard navigation

### Performance

- Efficient rendering
- Initial virtualization support or architecture prepared for virtualization
- Benchmarks

### Tooling

- Unit tests
- Integration tests
- Type checking
- Linting
- Formatting
- Package builds

### Documentation

- Installation
- Quick start
- Feature examples
- API reference

---

# 45. Explicitly Out of MVP

Do NOT implement:

- Vue adapter
- Svelte adapter
- Angular adapter
- Pivot tables
- Advanced aggregation
- Excel integration
- Complex charting
- Enterprise licensing
- Authentication
- Backend services
- Cloud infrastructure
- Analytics
- Telemetry

These may be considered later.

---

# 46. Development Milestones

## Milestone 0 — Architecture

Before coding:

1. Inspect repository.
2. Research existing dependencies.
3. Analyze TanStack Table and TanStack Virtual.
4. Define core architecture.
5. Define public API.
6. Define package boundaries.
7. Identify architectural risks.
8. Produce implementation plan.

Do not modify source code during this phase.

---

## Milestone 1 — Repository

Create:

```text
packages/
  core/
  react/

apps/
  demo/
  docs/
```

Configure:

- TypeScript
- Build system
- Testing
- Linting
- Formatting
- Workspace management

---

## Milestone 2 — Core

Implement:

- Grid state
- Data model
- Column definitions
- Sorting
- Filtering
- Pagination
- Selection

No UI dependencies.

---

## Milestone 3 — React Adapter

Implement:

```tsx
<DataGrid />;
```

with:

- Rendering
- Custom cells
- Headers
- Rows
- Selection
- Sorting
- Filtering
- Pagination

---

## Milestone 4 — Column Management

Implement:

- Resize
- Reorder
- Visibility
- Pinning

---

## Milestone 5 — Performance

Implement and benchmark:

- Virtualization
- Large datasets
- Render optimization

---

## Milestone 6 — Editing

Implement:

- Cell editing
- Row editing
- Validation
- Async save
- Custom editors

---

## Milestone 7 — Accessibility

Audit and improve:

- Keyboard navigation
- Focus management
- ARIA
- Screen readers

---

## Milestone 8 — Documentation

Build:

- Documentation site
- API reference
- Examples
- Tutorials

---

## Milestone 9 — Release Preparation

Prepare:

- npm packages
- Versioning
- Changelog
- README
- License
- Publishing workflow

---

# 47. Definition of Done

The MVP is ready when a developer can:

- Install the package.
- Define typed columns.
- Render a realistic dataset.
- Sort columns.
- Filter data.
- Paginate data.
- Select rows.
- Resize columns.
- Reorder columns.
- Hide columns.
- Customize cells.
- Edit cells.
- Use controlled state.
- Use server-side data.
- Navigate using keyboard.
- Customize styling.
- Understand the API from the documentation.

The resulting product should feel like a real developer tool, not a demo
project.

---

# 48. Agent Instructions

You are acting as the lead engineer for this project.

Follow this workflow.

## Phase 1 — Understand

Before modifying code:

1. Inspect the repository.
2. Inspect package configuration.
3. Inspect existing source files.
4. Inspect existing dependencies.
5. Understand the current architecture.

## Phase 2 — Research

Investigate relevant existing technologies.

At minimum evaluate:

- TanStack Table
- TanStack Virtual
- React
- TypeScript
- modern package/build tooling

Evaluate:

- API
- architecture
- license
- bundle size
- performance
- extensibility
- suitability for this product

Do not assume a dependency should be used.

## Phase 3 — Design

Produce:

1. Architecture diagram.
2. Package structure.
3. Core API proposal.
4. React API proposal.
5. State model.
6. Dependency decisions.
7. Testing strategy.
8. Build strategy.
9. Performance strategy.
10. Risks and tradeoffs.

## Phase 4 — Approval

Do not begin implementation until the architecture proposal has been presented
and approved.

---

# 49. Important Engineering Rules

Do not:

- Build everything at once.
- Create speculative abstractions.
- Couple core to React.
- Couple core to DOM.
- Couple core to networking.
- Add unnecessary dependencies.
- Implement future adapters during MVP.
- Build licensing before product validation.
- Optimize without benchmarks.
- Sacrifice accessibility for convenience.
- Expose internal implementation details as public API.

Prefer:

- Small modules
- Strong types
- Explicit APIs
- Testable logic
- Framework-independent state
- Measured performance
- Incremental implementation
- Clear documentation

---

# 50. First Task

Your FIRST task is NOT to implement the Data Grid.

Your first task is to analyze the project and produce an architecture proposal.

The proposal must answer:

1. What should `@portentgrid/core` contain?
2. What should `@portentgrid/react` contain?
3. Should TanStack Table be used?
4. Should TanStack Virtual be used?
5. What should the public API look like?
6. How should state flow between core and adapters?
7. How should rendering work?
8. How will future Vue/Svelte adapters consume the core?
9. What dependencies are actually necessary?
10. What should the monorepo structure be?
11. What are the major performance risks?
12. What are the major architectural risks?
13. What should be deliberately excluded from MVP?

Do not modify source code during this first phase.

Wait for approval before implementation.
