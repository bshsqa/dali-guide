---
id: grid-layout
title: "GridLayout"
sidebar_label: "GridLayout"
---
## Introduction to [GridLayout](./grid-layout.md)

`GridLayout` is a specialized layout container within the DALi GUI framework designed to organize child actors into a rigid, tabular structure of rows and columns. Use `GridLayout` when you require precise control over spatial positioning where content must align both horizontally and vertically, such as in dashboards, application grids, or complex forms. Unlike simpler linear arrangements, `GridLayout` allows for complex item placement, including row and column spanning, making it the primary choice for structured, matrix-based UI design.

## Defining Grid Structure

Establishing the grid architecture is achieved by defining the dimensions and count of rows and columns, which act as the constraints for all child elements. You must define these structures to instruct the layout engine on how to partition the available area.

### Defining Rows and Columns
The `AddRowDefinition` and `AddColumnDefinition` methods allow you to append individual constraints, while `SetRowDefinitions` and `SetColumnDefinitions` are used for bulk initialization.

* **AddRowDefinition(GridLength height)**: Appends a row with a specified `GridLength`.
* **SetRowDefinitions(const std::vector<GridLength>& rows)**: Replaces current row configuration with the provided vector.

```cpp
using namespace Dali::Ui;

void SetupGrid(GridLayout& grid)
{
  // Define 3 rows and 2 columns
  grid.AddRowDefinition(GridLength(100.0f)); // Fixed height
  grid.AddRowDefinition(GridLength(1.0f, GridLength::Type::RELATIVE));
  grid.AddRowDefinition(GridLength(100.0f));
  
  std::vector<GridLength> cols = {GridLength(50.0f), GridLength(50.0f)};
  grid.SetColumnDefinitions(cols);
}
```

> Note: Row and column definitions are processed by the layout engine during the next sync phase. Changing these while the grid is visible will trigger a relayout of all contained children.

## Managing Spacing and Alignment

To improve visual clarity and separation between cells, `[GridLayout](./grid-layout.md)` provides dedicated properties for controlling internal margins. You can manage these globally to ensure consistent spacing throughout the component.

### Configuring Inter-cell Gaps
Use `SetRowSpacing` and `SetColumnSpacing` to define the empty space between rows and columns respectively. The builder-pattern equivalents `RowSpacing()` and `ColumnSpacing()` are also provided for fluent initialization.

```cpp
GridLayout grid = GridLayout::New();

// Set 10 pixels gap between rows and 20 pixels between columns
grid.SetRowSpacing(10.0f);
grid.SetColumnSpacing(20.0f);

// Alternatively, using the fluent interface:
grid.RowSpacing(10.0f).ColumnSpacing(20.0f);
```

## Lifecycle and Initialization

`[GridLayout](./grid-layout.md)` follows the standard DALi handle-based lifecycle. It is a reference-counted object; creating a new instance returns a handle that manages the underlying engine-side object.

### Object Creation
Always use the static `New()` method to instantiate the layout. Avoid manual memory management as the object is automatically destroyed when the last handle goes out of scope.

```cpp
// Correct initialization
GridLayout myGrid = GridLayout::New();

// Using the handle in a builder context
GridLayout layout = GridLayout::New()
  .Rows({GridLength(1.0f), GridLength(1.0f)})
  .Columns({GridLength(1.0f), GridLength(1.0f)});
```

> Warning: Copying `[GridLayout](./grid-layout.md)` handles is inexpensive, as it simply increments the reference count. However, ensure that you are not holding stale handles if the grid has been removed from the UI tree.

## Dynamic Grid Manipulation

The layout engine supports runtime modification of the grid's structure. You can add, clear, and modify the grid definitions at any point after the layout has been created.

### Modifying Definitions
If the content of your UI changes (e.g., adding a new row to a table), use the clear and set methods to update the grid's blueprint.

* **ClearRowDefinitions() / ClearColumnDefinitions()**: Removes all existing constraints, effectively resetting the grid structure.

```cpp
void RefreshGrid(GridLayout& grid)
{
  // Reset existing structure
  grid.ClearRowDefinitions();
  grid.ClearColumnDefinitions();
  
  // Re-define for new data size
  grid.AddRowDefinition(GridLength(50.0f));
  grid.AddColumnDefinition(GridLength(50.0f));
}
```

## Integration and Casting

`[GridLayout](./grid-layout.md)` operates in conjunction with `GridLayoutParams`, which defines how a specific child fits within the parent grid. You must provide `GridLayoutParams` to child actors so the layout engine knows which cell they occupy.

### Using GridLayoutParams
Each child added to the `[GridLayout](./grid-layout.md)` should be associated with parameters that determine its `Row`, `Column`, `RowSpan`, and `ColumnSpan`.

```cpp
// Configure parameters for a child actor
GridLayoutParams params;
params.SetRow(0)
      .SetColumn(0)
      .SetRowSpan(1)
      .SetColumnSpan(2); // Spans two columns

// Assign these parameters to your child actor via the parent's layout integration
// → See: [LayoutContainer] for attaching parameters to children.
```

### Casting
If you receive a base `[Layout](./layout.md)` handle, use `DownCast` to safely obtain a `[GridLayout](./grid-layout.md)` handle. This ensures type safety when interacting with specific grid functionality.

```cpp
void ProcessLayout(BaseHandle handle)
{
  GridLayout grid = GridLayout::DownCast(handle);
  if(grid)
  {
    // It is safe to use grid-specific methods
    uint32_t rows = grid.GetRowCount();
  }
}
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/grid-layout)
