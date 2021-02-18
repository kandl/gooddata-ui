---
title: Pivot Table
sidebar_label: Pivot Table
copyright: (C) 2020 GoodData Corporation
id: version-7.8.0-pivot_table_component
original_id: pivot_table_component
---

Pivot table component expands capabilities of a regular (flat) table by allowing you to reorganize and summarize selected data beyond the typical row-column relationship.

In GoodData.UI, a pivot table allows you to break measures into columns by setting attributes in the ```columns``` prop. You can also choose to display only attributes (without any measures). On the other hand, a flat table cannot display attributes in columns.

Compared to charts, pivot tables have higher limits for the number of datapoints to display.

The following pivot table shows franchise fees (a measure), which are split down by location state (an attribute) horizontally and by month (a column attribute) vertically.

![Pivot Table Component](assets/pivot_table_description.png "Pivot Table Component")

## Structure

```jsx
import '@gooddata/react-components/styles/css/main.css';
import { PivotTable } from '@gooddata/react-components';

<PivotTable
    projectId={<project-id>}
    measures={<measures>}
    sdk={<sdk>}
    …
/>
```

## Examples

### Pivot table

The following code sample shows an arrangement for a typical pivot table.

```jsx
const measures = [
    {
        measure: {
            localIdentifier: 'franchiseFees',
            definition: {
                measureDefinition: {
                    item: {
                        identifier: franchiseFeesIdentifier
                    }
                }
            },
            format: '#,##0'
        }
    }
];

const columns = [
    {
        visualizationAttribute: {
            displayForm: {
                identifier: monthDateIdentifier
            },
            localIdentifier: 'month'
        }
    }
];

const rows = [
    {
        visualizationAttribute: {
            displayForm: {
                identifier: locationStateDisplayFormIdentifier
            },
            localIdentifier: 'location'
        }
    }
];

<div style={{ height: 300 }}>
    <PivotTable
        projectId={projectId}
        measures={measures}
        rows={rows}
        columns={columns}
    />
</div>
```

### Flat table

You can also use the pivot table component to create a regular (flat) table.

```jsx
const measures = [
    {
        measure: {
            localIdentifier: 'franchiseFees',
            definition: {
                measureDefinition: {
                    item: {
                        identifier: franchiseFeesIdentifier
                    }
                }
            },
            format: '#,##0'
        }
    }
];

const rows = [
    {
        visualizationAttribute: {
            displayForm: {
                identifier: locationStateDisplayFormIdentifier
            },
            localIdentifier: 'location'
        }
    }
];

<div style={{ height: 300 }}>
    <PivotTable
        projectId={projectId}
        measures={measures}
        rows={rows}
        columns={columns}
    />
</div>
```

## Sorting in pivot tables

You can [sort](50_custom__result.md#sorting) rows and attribute columns in any pivot table. Measures are always listed in the same order in which they were defined in the ```measures``` prop.

**Important!** Sorting must be applied to any column attribute that is used in the pivot table. For example, in the following table, you apply sorting to both the Franchise Fees (measure) and the Date (column attribute).

### Example: Sorting by measure

```jsx
// ...using Pivot Table Example

const sortBy = [
    {
        measureSortItem: {
            direction: 'desc',
            locators: [
                {
                    attributeLocatorItem: {
                        attributeIdentifier: 'month',
                        element: monthDateIdentifierJanuary
                    }
                },
                {
                    measureLocatorItem: {
                        measureIdentifier: 'franchiseFeesIdentifier'
                    }
                }
            ]
        }
    }
];

<div style={{ height: 300 }}>
    <PivotTable
        projectId={projectId}
        measures={measures}
        rows={rows}
        columns={columns}
        sortBy={sortBy}
    />
</div>
```

## Grouping

By default, the same row attribute values in a column are grouped across the rows, which helps improve data readability in pivot tables. The header of the grouped columns is displayed at the top of the table even when you scroll through a long group.

The grouping is applied when the following conditions are both met:
* The table is sorted by the **first** row attribute (either in ascending or descending order).
* The table has **two or more** row attributes.

![Pivot Table Grouping](assets/pivot_table_grouping.png "Pivot Table Grouping")

To disable grouping, set the `groupRows` property to `false` (see [Properties](#properties)).

## Totals

You can display rows with aggregated measure data using the `totals` prop. For more information, see [Specify Table Totals](30_tips__table_totals.md).

Alternatively, you can enable the menu through which you can turn on the totals and subtotals. For more information, see [Configuration menu](#configuration-menu).

![Pivot Table Menu Totals](assets/pivot_table_menu_totals.png "Pivot Table Menu Totals")

## Maximum height

By default, the maximum hight is not specified, and the pivot table fills the whole container. If there is still some empty space within the container after all the rows have been displayed, and the table has a total specified, a gap may appear between the data and the total because the total row sticks to the bottom.

To avoid this gap, specify the maximum height of the table using the `maxHeight` prop. With the `maxHeight` set, the table is displayed as small as possible while not expanding beyond the `maxHeight` limit, thus avoiding the gap. For more information, see [Configuration menu](#configuration-menu).

**NOTE:** The `maxHeight` must be specified in pixels. If you want your table to be responsive, consider using [react-measure](https://github.com/souporserious/react-measure) to derive the `maxHeight` value dynamically.

## Column width resizing

### Auto resizing

By default, the width of the columns is not set, and all columns have the same fixed width regardless of the actual content.

To automatically resize the columns to fit their content, add the `columnSizing` prop and set it to `defaultWidth: "viewport"`:
```jsx
columnSizing: {
    defaultWidth: "viewport"
}
```
* The size is calculated based on the content in the header of the column that represents the lowest level of the grouped attributes (see [Grouping](#grouping)). If this is not applicable, the size is calculated based on the content in the header of the column with the measure name and the cells with the measure values.
* The maximum column width is 500 px.
* Only the columns that are visible during the initial rendering of the table are automatically resized to fit their content.
* New columns (that is, those that are shown after the initial render) are resized only if the table has not been vertically or horizontally scrolled. This is useful especially for responsive tables with a dynamic width.
* A change of attributes, measures, filters, or totals in the table is handled as a new table. After the change is made, the column size is re-calculated based on the new data.
* Scrolling horizontally or vertically and sorting values in a column do not affect the column width.
* If you manually adjust the column width, the adjusted width is preserved only temporarily and will be reset to the previously set value after the table is re-rendered.

### Manual resizing

To set the width of the columns, add the `columnWidths` prop to the `columnSizing` prop.

```jsx
columnSizing: {
   columnWidths: [
        {
            attributeColumnWidthItem: {
                width: { value: 100 },
                attributeIdentifier: 'day'
            }
        },
        {
            measureColumnWidthItem: {
                width: { value: 200 },
                locators: [
                    {
                        attributeLocatorItem: {
                            attributeIdentifier: 'month',
                            element: monthDateIdentifierJanuary
                        }
                    },
                    {
                        measureLocatorItem: {
                            measureIdentifier: 'franchiseFeesIdentifier'
                        }
                    }
                ]
            }
        }
   ]
}
```
* The width of the table columns is set according to the provided column width definitions.
* The width specified in a width definition or set by resizing the column manually in the UI can be between 60 px (minimum width) and 2000 px (maximum width). If the width is set outside of this range, the closest limit value is used instead.
* Attribute and measure identifiers in width definitions are partially validated to match the items used in the table. If validation cannot be performed upon some width definitions, those width definitions are ignored.
* If the column width definitions change, the table is re-rendered with the new column width definitions.
* Scrolling horizontally or vertically, sorting values in a column, or adding totals do not affect the column width.
* Changing the `measures`, `rows`, `columns`, or `filters` props in a table discards any changes in the column width done by resizing the columns manually in the UI. After that, you can manually resize the columns in the UI back to the preferable width.

    To get notified about the change in the width of columns done by resizing the column manually in the UI, add the `onColumnResized` prop with a callback function to the table props:
    ```html
    <PivotTable
        projectId={projectId}
        measures={measures}
        rows={rows}
        columns={columns}
        sortBy={sortBy}
        config={config}
        onColumnResized={handleOnColumnResized}
    />
    ```

    A change of the column width calls the provided callback function with all the current column width definitions as a parameter.
* To set the same width for all measure columns, add the `allMeasureColumnWidthItem` prop:
    ```jsx
    const allMeasureColumnWidthItem = {
        measureColumnWidthItem: {
            width: { value: 200 },
        }
    };
    columnSizing: {
        columnWidths: [
            allMeasureColumnWidthItem
        ]
    }
    ```

* To set the same width for all columns of a specific measure (applicable when column attributes are used), add the `weakMeasureColumnWidthItem` prop:
    ```jsx
    const weakMeasureColumnWidthItem = {
        measureColumnWidthItem: {
            width: { value: 200 },
            locator: {
                measureLocatorItem: {
                    measureIdentifier: 'franchiseFees'
                }
            }
        }
    };
    columnSizing: {
        columnWidths: [
            weakMeasureColumnWidthItem
        ]
    }
    ```

**TIP:** Instead of creating `attributeColumnWidthItem`, `measureColumnWidthItem`, `allMeasureColumnWidthItem` or `weakMeasureColumnWidthItem` manually, you can use the [width item helpers](02_start__execution_model.md#width-item-helpers-for-pivot-tables).

### Priorities of column width definitions

`measureColumnWidthItem` defined for a specific column overrides the value set by `weakMeasureColumnWidthItem` for a group of columns of one measure. `measureColumnWidthItem` for this column also overrides the value specified by `allMeasureColumnWidthItem` for all measure columns.

### Combining auto resizing and manual resizing

To combine auto resizing and manual resizing, add both the `defaultWidth` and `columnWidths` props under the `columnSizing` prop.

**Example 1:** In the following code sample:
* The width of the columns that are defined under `columnWidths` is set according to the values of their appropriate `width` props (see [Manual resizing](#manual-resizing)).
* All the other columns are resized to fit the content (see [Auto resizing](#auto-resizing)).

```jsx
columnSizing: {
    defaultWidth: "viewport",
    columnWidths: [
        {
            attributeColumnWidthItem: {
                width: { value: 100 },
                attributeIdentifier: 'day'
            }
        },
        {
            measureColumnWidthItem: {
                width: { value: 200 },
                locators: [
                   {
                        attributeLocatorItem: {
                            attributeIdentifier: 'month',
                            element: monthDateIdentifierJanuary
                        }
                   },
                   {
                        measureLocatorItem: {
                            measureIdentifier: 'franchiseFeesIdentifier'
                        }
                   }
                ]
            }
        }
    ]
}
```

**Example 2:** In the following code sample:
* The width of all the measure columns is set to the value of the `width` prop under `allMeasureColumnWidthItem` (see [Manual resizing](#manual-resizing)).
* However, the `measureColumnWidthItem` prop overrides the value set by `allMeasureColumnWidthItem` for the measure columns that are defined under `measureColumnWidthItem`. Notice that the `width` prop under `measureColumnWidthItem` is set to `"auto"` and not to a number as in **Example 1**. This means that at the initial rendering these measure columns will be resized to fit the content (see [Auto resizing](#auto-resizing)), while all the other measure columns will be set to the width defined by `allMeasureColumnWidthItem`.
* All the attribute columns, if any, are resized to fit the content (see [Auto resizing](#auto-resizing)).

```jsx
columnSizing: {
    defaultWidth: "viewport",
    columnWidths: [
        {
            measureColumnWidthItem: {
                width: { value: 200 },
            }
        },
        {
            measureColumnWidthItem: {
                width: { value: "auto" },
                locators: [
                   {
                        attributeLocatorItem: {
                            attributeIdentifier: 'month',
                            element: monthDateIdentifierJanuary
                        }
                   },
                   {
                        measureLocatorItem: {
                            measureIdentifier: 'franchiseFeesIdentifier'
                        }
                   }
                ]
            }
        }
    ]
}
```

**Example 3:** In the following code sample:
* The width of all the measure columns is set to the value of the `width` prop under `allMeasureColumnWidthItem` (see [Manual resizing](#manual-resizing)).
* The width of all columns of the selected measure is set to the value of the `width` prop under `weakMeasureColumnWidthItem` and overrides value from `allMeasureColumnWidthItem` (see [Manual resizing](#manual-resizing)).
* However, the `measureColumnWidthItem` prop overrides the value set by `allMeasureColumnWidthItem` and `weakMeasureColumnWidthItem` for the measure column that is defined under `measureColumnWidthItem`. Notice that the `width` prop under `measureColumnWidthItem` is set to `"auto"` and not to a number as in **Example 1**. This means that at the initial rendering this measure column will be resized to fit the content (see [Auto resizing](#auto-resizing)), while all the other measure columns will be set to the width defined by `allMeasureColumnWidthItem` or `weakMeasureColumnWidthItem`.
* All the attribute columns, if any, are resized to fit the content (see [Auto resizing](#auto-resizing)).

```jsx
columnSizing: {
    defaultWidth: "viewport",
    columnWidths: [
        {
            measureColumnWidthItem: {
                width: { value: 200 },
            }
        },
        {
            measureColumnWidthItem: {
                width: { value: 400 },
                locator: {
                    measureLocatorItem: {
                        measureIdentifier: 'totalSalesIdentifier'
                    }
                }
            }
        },
        {
            measureColumnWidthItem: {
                width: { value: "auto" },
                locators: [
                   {
                        attributeLocatorItem: {
                            attributeIdentifier: 'month',
                            element: monthDateIdentifierJanuary
                        }
                   },
                   {
                        measureLocatorItem: {
                            measureIdentifier: 'franchiseFeesIdentifier'
                        }
                   }
                ]
            }
        }
    ]
}
```

### Resizing visible columns to fill up the container

To make the currently visible columns take the available screen space, add the `growToFit` prop to the `columnSizing` object and set it to `true`.

```jsx
columnSizing: {
    defaultWidth: "viewport",
    growToFit: true
}
```

**NOTES:**
* This behavior is not applied to the [manually resized columns](#manual-resizing). It is applied only to the columns that are **not** inside the `columnWidths` object.
* If [auto resizing](#auto-resizing) is enabled, the columns that are visible during the initial rendering of the table are automatically resized to fit their content. If these columns do not fit the screen, they will be resized to fill up the container.

### Resizing a column to a custom width

1. Hover over the right side of the column header until a horizontal resize cursor appears.
2. Drag the column line.

    The column is resized to the set width.

    The new column width is propagated via the `onColumnResized` callback array.

### Resizing all measure columns at once to a custom width

1. Hover over the right side of the header of any measure column until a horizontal resize cursor appears.
2. Press and hold **Ctrl** or **Command**, and drag the column line.

    All the measures columns are resized to the set width.

    The new column widths are propagated via the `onColumnResized` callback array. All the `measureColumnWidthItem` props are removed. The `allMeasureColumnWidthItem` prop is added.

### Resizing all columns of a specific measure at once to a custom width

1. Hover over the right side of the header of any column of the specific measure until a horizontal resize cursor appears.
2. Press and hold **Alt** or **Option**, and drag the column line.

    All columns of the corresponding measure are resized to the set width.

    The new column widths are propagated via the `onColumnResized` callback array. All the `measureColumnWidthItem` props of the corresponding measure are removed. The `weakMeasureColumnWidthItem` prop is added.

### Resizing a column to fit its content

1. Hover over the right side of the column header until a horizontal resize cursor appears.
2. Double-click the column line.

    The column is resized. The width is calculated to fit the content of all loaded rows in this column.

    The new column width is propagated via the `onColumnResized` callback array.

**NOTES:**
* This behavior is not applied if [auto resizing](#auto-resizing) is enabled and you double-click a column that was visible and auto-resized at the initial rendering and then its width was manually adjusted in the UI. Such column is removed from the `onColumnResized` callback array.
* If [auto resizing](#auto-resizing) is enabled, and `columnWidths` includes the `allMeasureColumnWidthItem` or `weakMeasureColumnWidthItem` prop, and you double-click a measure column, the `measureColumnWidthItem` prop with `width` set to `"auto"` is added to the `onColumnResized` callback array.

### Resizing all measure columns at once to fit their content

1. Hover over the right side of the header of any measure column until a horizontal resize cursor appears.
2. Press and hold **Ctrl** or **Command**, and double-click the column line.

    All measure columns are resized to fit their content.

    The new column widths are propagated via the `onColumnResized` callback array. All the `allMeasureColumnWidthItem` props are removed.

### Resizing all columns of a specific measure at once to fit their content

1. Hover over the right side of the header of any column of the specific measure until a horizontal resize cursor appears.
2. Press and hold **Alt** or **Option**, and double-click the column line.

    All columns of the corresponding measure are resized to fit their content.

    The new column widths are propagated via the `onColumnResized` callback array. All the `weakMeasureColumnWidthItem` props are removed.

### Switching to the default resizing

To switch to the default behavior (all columns have the same fixed size), do not provide `columnSizing` at all or set `columnSizing` to:
```jsx
columnSizing: {
    defaultWidth: "unset",
    columnWidths: undefined
}
```
You can omit the `columnWidths` prop completely. It has the same effect as specifying `columnWidths: undefined`.

## Configuration menu

You can configure the following settings:
* **Totals** and **subtotals**. If you enable the subtotals menu but disable totals, subtotals will be disabled too.
* **Separators** used when formatting numbers. See [Change a separator in the number format](15_props__chart_config.md#change-a-separator-in-the-number-format).
* **Maximum height**. See [Maximum height](#maximum-height).
* **Column width resizing**. See [Column width resizing](#column-width-resizing).

```jsx
const config = {
    maxHeight: 800,
    menu: {
        aggregations: true,
        aggregationsSubMenu: true
    },
    separators: {
        thousand: ',',
        decimal: '.'
    },
    columnSizing: {
        defaultWidth: "viewport",
        columnWidths: [
            {
                attributeColumnWidthItem: {
                    width: 100,
                    attributeIdentifier: 'day'
                }
            },
            {
                measureColumnWidthItem: {
                    width: 100,
                    locators: [
                        {
                            attributeLocatorItem: {
                                attributeIdentifier: 'month',
                                element: monthDateIdentifierJanuary
                            }
                        },
                        {
                            measureLocatorItem: {
                                measureIdentifier: 'franchiseFeesIdentifier'
                            }
                        }
                    ]
                }
            }
        ]
    }
};

<PivotTable
    projectId={projectId}
    measures={measures}
    rows={rows}
    columns={columns}
    sortBy={sortBy}
    config={config}
/>
```

## Properties

| Name | Required? | Type | Description |
| :--- | :--- | :--- | :--- |
| projectId | true | string | The project ID |
| measures | false | [Measure[]](50_custom__execution.md#measure) | An array of measure definitions (either measures, or rows, or columns must be provided for the pivot table to render properly) |
| rows | false | [Attribute[]](50_custom__execution.md#attribute) | An array of attribute definitions that breaks measure data into rows (either measures, or rows, or columns must be provided for the pivot table to render properly) |
| columns | false | [Attribute[]](50_custom__execution.md#attribute) | An array of attribute definitions that breaks measure data into columns (either measures, or rows, or columns must be provided for the pivot table to render properly) |
| totals | false | [Total[]](30_tips__table_totals.md) | An array of total definitions |
| filters | false | [Filter[]](30_tips__filter_visual_components.md) | An array of filter definitions |
| config | false | [ConfigObject](#configuration-menu) | The configuration object |
| sortBy | false | [SortItem[]](50_custom__result.md#sorting) | An array of sort definitions |
| groupRows | false | boolean | Specifies whether [grouping of the same values in attribute columns](#grouping) is enabled (`true`; default) or disabled (`false`). |
| locale | false | string | The localization of the table. Defaults to `en-US`. For other languages, see the [full list of available localizations](https://github.com/gooddata/gooddata-react-components/tree/master/src/translations). |
| drillableItems | false | [DrillableItem[]](15_props__drillable_item.md) | An array of points and attribute values to be drillable. |
| sdk | false | SDK | A configuration object where you can define a custom domain and other API options |
| ErrorComponent | false | Component | A component to be rendered if this component is in error state. See [ErrorComponent](15_props__error_component.md).|
| LoadingComponent | false | Component | A component to be rendered if this component is in loading state. See [LoadingComponent](15_props__loading_component.md).|
| onError | false | Function | A callback when the component updates its error state |
| onExportReady | false | Function | A callback when the component is ready for exporting its data |
| onLoadingChanged | false | Function | A callback when the component updates its loading state |

<!-- These internals are intentionally undocumented
| afterRender | false | Function | A callback after component is rendered |
| dataSource | false | DataSource class | A class that is used to resolve AFM |
| height | false | number | Height of the component in pixels |
| pushData | false | Function | A callback after AFM is resolved |
-->
