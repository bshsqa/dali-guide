---
id: grid-layout
title: "GridLayout"
sidebar_label: "GridLayout"
---
## Introduction to [GridLayout](./grid-layout.md)

`GridLayout` is a specialized layout component designed to arrange UI elements within a structured, matrix-like grid of rows and columns. It provides a flexible way to align and distribute child elements, making it ideal for dashboard-style interfaces, form [layouts](./layouts.md), or any UI requiring consistent alignment across different screen sizes.

Unlike linear [layouts](./layouts.md) that stack elements along a single axis, `GridLayout` allows you to explicitly define row and column dimensions, making it distinct for its ability to manage two-dimensional spatial distribution. Use `GridLayout` when you require strict control over child positioning where elements must occupy specific cells or span across multiple cells.

## Creating and Instantiating the [Layout](./layout.md)

The `GridLayout` class is instantiated using the static `New()` factory method, which returns a handle to a new, initialized instance. It follows standard DALi [object](./object.md) lifecycle patterns, including copy and move constructors for efficient handle management.

### Instantiation
To create a grid, call the static `New()` method.

```cpp
using namespace Dali::Ui;

// Create a new GridLayout
GridLayout grid = GridLayout::New();
```

> Note: Always use the `New()` method to create instances. The default constructor creates an empty, uninitialized handle; attempting to use an uninitialized handle will result in undefined behavior.

## Defining Grid Structure

Defining the grid structure involves specifying the height of rows and the width of columns. `[GridLayout](./grid-layout.md)` allows you to either append definitions one by one or apply a batch update using vectors.

### Row and Column Definitions
You can use `AddRowDefinition()` and `AddColumnDefinition()` to build your grid incrementally, or `SetRowDefinitions()` and `SetColumnDefinitions()` for bulk configuration.

```cpp
// Defining a 2x2 grid
GridLayout grid = GridLayout::New();

// Adding rows and columns individually
grid.AddRowDefinition(GridLength(100.0f)); // Fixed height row
grid.AddColumnDefinition(GridLength(1.0f, GridLength::Type::RELATIVE)); // Flexible column

// Alternatively, use batch assignment
std::vector<GridLength> rows = {GridLength(50.0f), GridLength(50.0f)};
grid.SetRowDefinitions(rows);
```

### Fluent Configuration
`[GridLayout](./grid-layout.md)` provides fluent-style methods like `Rows()` and `Columns()` which return a reference to the layout, allowing for chainable configurations.

```cpp
GridLayout grid = GridLayout::New()
    .Rows({GridLength(1.0f), GridLength(1.0f)})
    .Columns({GridLength(1.0f), GridLength(1.0f)})
    .RowSpacing(10.0f)
    .ColumnSpacing(5.0f);
```

## Managing Spacing and Alignment

Precise visual control is achieved through row and column spacing, as well as per-child alignment parameters. While the layout handles the container-level spacing, individual child placement is managed via the `GridLayoutParams` class.

### Setting Grid Spacing
Spacing controls the gaps between rows and columns, providing necessary breathing room between grid cells.

```cpp
// Set vertical and horizontal gaps
grid.SetRowSpacing(20.0f);
grid.SetColumnSpacing(15.0f);
```

### Configuring Child Placement
To position a child within the grid, you must use `GridLayoutParams`. This class informs the `[GridLayout](./grid-layout.md)` about which row and column a child should occupy, and how it should span across them.

```cpp
GridLayoutParams params;
params.SetRow(0)
      .SetColumn(0)
      .SetRowSpan(1)
      .SetColumnSpan(2)
      .SetHorizontalAlignment(LayoutAlignment::CENTER);

// Assign params to a child element managed by the layout
child.SetLayoutParameters(params);
```

## Querying Grid State

You can retrieve the current configuration of the grid to dynamically adjust UI logic based on the layout's state.

### Retrieving Dimensions
The `GetRowCount()`, `GetColumnCount()`, `GetRowDefinitions()`, and `GetColumnDefinitions()` methods allow you to inspect the grid's structure at runtime.

```cpp
uint32_t rows = grid.GetRowCount();
uint32_t cols = grid.GetColumnCount();

if (rows > 0) {
    auto definitions = grid.GetRowDefinitions();
    // Logic to respond to current row configuration
}
```

## Best Practices for Grid Implementation

To ensure responsive and efficient UI design when using `[GridLayout](./grid-layout.md)`, follow these implementation patterns:

* **Initialization:** Always initialize the layout before attaching it to a container.
* **Layout Cleanup:** If the grid structure changes dynamically (e.g., in response to orientation changes), use `ClearRowDefinitions()` and `ClearColumnDefinitions()` before applying new definitions to avoid unexpected layout calculations.
* **Responsive Spacing:** When using relative `GridLength` values, ensure that spacing values are set to absolute units (pixels) to maintain a consistent visual gap regardless of screen density.
* **Handle Management:** Since `[GridLayout](./grid-layout.md)` is a handle-based class, it is safe to copy and move instances freely without deep-copying the underlying layout state.

> Warning: Performance may degrade if you frequently update large grid definitions within a single frame. Batch your definition updates using `SetRowDefinitions()` and `SetColumnDefinitions()` instead of adding them one-by-one whenever possible.

→ See: [Layouts]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/grid-layout)
