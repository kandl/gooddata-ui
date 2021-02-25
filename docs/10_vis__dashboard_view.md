---
title: DashboardView
sidebar_label: DashboardView
copyright: (C) 2007-2018 GoodData Corporation
id: dashboard_view_component
---

> **The DashboardView component is in the beta stage.**
>
> The component may be changed in future releases, even in a backward incompatible way.

The **DashboardView component** is a generic component that renders dashboards created and saved by KPI Dashboards.
It allows you to embed the dashboard natively in React _in view mode_ (similarly to [InsightView](10_vis__insight_view.md) for visualizations).

It also provides mechanisms to allow you to integrate it with the rest of your application.
For example, you can provide your custom filtering UI (see [Integration with your application](#integration-with-your-application)).
The users with the appropriate permissions can also set [KPI Alerts](#kpi-alerts) and create [Scheduled emails](#scheduled-emails) (unless you enable [Read-only mode](#read-only-mode)).

You can also customize the way the dashboard is rendered – you can alter the layout and change the way particular widgets are rendered (see [Customizations](#customizations)).

## Structure

```jsx
import "@gooddata/sdk-ui-ext/styles/css/main.css";
import { DashboardView } from "@gooddata/sdk-ui-ext";

<DashboardView dashboard="<dashboard-identifier>" />;
```

```jsx
import "@gooddata/sdk-ui-ext/styles/css/main.css";
import { DashboardView } from "@gooddata/sdk-ui-ext";
import { uriRef } from "@gooddata/sdk-model";

<DashboardView dashboard={uriRef("<dashboard-uri>")} />;
```

## Filters

The DashboardView will respect any filters set for the dashboard in KPI Dashboards.
You can override these filters by providing a value for the `filters` prop.
These filters will then be used for

-   rendering the dashboard
-   setting up [KPI Alerts](#kpi-alerts)
-   creating [Scheduled emails](#scheduled-emails) (you can disable that by setting the `applyFiltersToScheduledMail` prop to `false`).

If you want to add some filters to the filters already specified on the dashboard, you can use the `mergeFiltersWithDashboard` function
TODO add DashboardViewWithMergedFilters link

For more information about the filters themselves, see [Filter Visual Components](30_tips__filter_visual_components.md).
Alternatively, you can pass filters specified using the [`FilterContextItem`](https://github.com/gooddata/gooddata-ui-sdk/blob/6ba2ed93163b830a6a0f03437861ac9ef1d423be/libs/sdk-backend-spi/src/workspace/dashboards/filterContext.ts#L133) type. The main difference between this and the standard filters is that FilterContextItem date filters do not specify a date dimension and so are applied on all the date dimensions (this is what the KPI Dashboards date filter is doing).

> **NOTE**: There is currently a limitation on the attribute filters: you can only use URIs to identify the attribute elements.

## Theming

DashboardView fully supports themes (see [Theme Provider](10_vis__theme_provider.md)) and it will try to obtain the theme configuration to use by the following algorithm:

1. If the `theme` prop was provided, its value will be used as the theme configuration.
1. If there is a ThemeProvider above the DashboardView, it will use the theme configuration provided by that ThemeProvider.
1. If there is no ThemeProvider, DashboardView will create its own ThemeProvider and it will try to get the theme from its workspace.
    - this can be disabled by setting the `disableThemeLoading` prop to `true` (defaults to `false`)

## Configuration

You can provide visualizations-specific configuration for the visualizations rendered by the DashboardView using the `config` prop:

-   `mapboxToken` – API token to be used by GeoPushpinCharts. See [Geo Config](10_vis__geo_pushpin_chart_component.md#geo-config).
-   `separators` – configuration for number formatting. See [Change a separator in the number format](15_props__chart_config.md#change-a-separator-in-the-number-format).
-   `locale` – locale to be used. Defaults to the locale set for the current user. For other languages, see the [full list of available localizations](https://github.com/gooddata/gooddata-ui-sdk/blob/master/libs/sdk-ui/src/base/localization/Locale.ts).
-   `disableKpiDrillUnderline` – if set to `true`, KPIs with drilling enabled will _not_ be underlined. Defaults to `false`.

## Drilling

DashboardView supports firing of drill events from drilling set up on the dashboard.
You can listen for drilling events by providing an `onDrill` prop (see [OnDrill](15_props__on_drill.md)).
You can also specify additional drillable items using the `drillableItems` prop (see [Drillable Items](15_props__drillable_item.md)).

## Scheduled emails

You can allow users to create new [Scheduled emails](https://help.gooddata.com/pages/viewpage.action?pageId=66202520) for the dashboard. You can display the dialog for setting up the Scheduled email by setting the `isScheduledMailDialogVisible` to true. There are also other props you can use to interact with the dialog:

-   `onScheduledMailDialogSubmit` – called when the user confirms the Scheduled email creation. The callback will receive the Scheduled email definition the user submitted as a parameter.
-   `onScheduledMailDialogCancel` – called when the user cancels or closes the Scheduled email dialog
-   `onScheduledMailSubmitSuccess` – called when the Scheduled email is created and stored on the server. The callback will receive the saved Scheduled email definition as a parameter.
-   `onScheduledMailSubmitError` – called when the Scheduled email creation fails. The callback will receive the error object as a parameter.

By default, the Scheduled emails created this way will be filtered by the filters passed in the `filters` prop if specified (see [Filters](#filters)) or fall back to the filters set up on the dashboard in KPI Dashboards. If you would prefer the Scheduled email to always use the filters that were set on the dashboard in KPI Dashboards, set the `applyFiltersToScheduledMail` to `false` (defaults to `true`).

Here is a simple example of how to handle the Scheduled emails dialog.

```jsx
import React, { useState } from "react";
import { DashboardView } from "@gooddata/sdk-ui-ext";
import "@gooddata/sdk-ui-ext/styles/css/main.css";

const DashboardViewWithEmails = () => {
    const [isEmailDialogOpen, setIsEmailDialogOpen] = useState(false);
    return (
        <>
            <button onClick={() => setIsEmailDialogOpen(true)}>
                Open Schedule Email Dialog
            </button>
            <DashboardView
                dashboard="dashboard-id"
                isScheduledMailDialogVisible={isEmailDialogOpen}
                onScheduledMailDialogCancel={() => setIsEmailDialogOpen(false)}
                onScheduledMailSubmitSuccess={() => {
                    alert("Scheduled email scheduled successfully");
                    setIsEmailDialogOpen(false);
                }}
                onScheduledMailSubmitError={() => {
                    alert("Scheduled email error");
                    setIsEmailDialogOpen(false);
                }}
            />
        </>
    );
};
```

## KPI Alerts

DashboardView supports displaying, creating, editing and deleting of [KPI Alerts](https://help.gooddata.com/pages/viewpage.action?pageId=34341718).
The Alerts will use the filters in the `filters` prop if provided (see [Filters](#filters)), or fall back to the filters set up on the dashboard in KPI Dashboards.

## Read-only mode

By default, DashboardView will allow users with appropriate permissions to create KPI Alerts and Scheduled emails. If you do not want this, you can set the `isReadonly` prop to `true` (defaults to `false`). This will completely disable the [KPI Alerts](#kpi-alerts) and [Scheduled emails](#scheduled-emails) features of DashboardView.

## Customizations

> **NOTE:** All the customizations described in this section are effective only for the given DashboardView. They will _not_ take effect in neither Scheduled emails, PDF exports nor emails coming from KPI Alerts.

When using DashboardView, you can customize the way individual dashboard widgets are rendered. You can do that by providing a [render prop](https://reactjs.org/docs/render-props.html) to the `widgetRenderer` prop.
It should return a piece of JSX representing the widget. It will be called with an object containing the following properties:

| Name             | Type                             | Description                                                                                                                |
| :--------------- | :------------------------------- | :------------------------------------------------------------------------------------------------------------------------- |
| widget           | IWidget                          | The information about the widget as specified in the dashboard                                                             |
| renderedWidget   | ReactElement                     | Widget as rendered by the default logic. You can use it to opt-out of customizations for a given widget by returning this. |
| insight          | IInsight or undefined            | Insight object relevant to the widget (if the widget is not a KPI).                                                        |
| alert            | IWidgetAlert or undefined        | Widget alert relevant to the widget (if the widget is a KPI and the user has some alert set).                              |
| filters          | FilterContextItem[] or undefined | Sanitized filters provided to the `filters` prop (if any)                                                                  |
| predicates       | IWidgetPredicates                | A set of [predicates](#predicates) provided to help you choose widgets for custom rendering                                |
| ErrorComponent   | Component                        | A component to be rendered if the widget is in error state (see [ErrorComponent](15_props__error_component.md))            |
| LoadingComponent | Component                        | A component to be rendered if the widget is in loading state (see [LoadingComponent](15_props__loading_component.md))      |

Ensure that your custom widgets are responsive and have height & width set to 100%.

For an example see TODO link to CustomDashboardView example.

### Predicates

The `predicates` property of the `widgetRenderer` only parameter contains a set of convenience functions that can help you decide which widgets you want to render which way. These are

-   `isWidgetWithRef(ref)` – to match a particular widget
-   `isWidgetWithInsightRef(ref)` – to match widgets with a particular insight
-   `isWidgetWithInsightType(type)` – to match widgets with insights with a particular visualization type
-   `isWidgetWithKpiRef(ref)` – to match widgets with a particular KPI
-   `isWidgetWithKpiType(comparisonType)` – to match widgets with KPI of a certain comparison type (e.g. Previous Period)
-   `isCustomWidget: ()` - to match widgets that are not commonly part of the dashboard (e.g. your custom widgets added in DashboardView `layoutTransform` callback)

### Auxiliary hooks

To make implementation of the custom renderers as easy as possible, we provide some convenience hooks that you can use to obtain the exactly same data the default renderer would:

-   `useDashboardWidgetExecution` – creates a PreparedExecution for a given widget
-   `useDataView` - executes a given PreparedExecution and returns the resulting data

See TODO examples link for an example how to use those.

## Layout

You can customize the dashboard layout (e.g. filter/reorder the widgets), or even extend it with you own custom widgets. This is possible via `transformLayout` callback.

The dashboard layout consists of sections. Each section can contain a header with a title and description and a subset of items. Each item consists of a size setting, and data for the widget. The total width of the layout grid is 12 columns, and currently there is no way to change it.

To make layout transformations more convenient, the `transformLayout` callback is called with the layoutBuilder parameter, which provides an interface with commonly needed methods for transformations (add/remove/modify).

```jsx
import "@gooddata/sdk-ui-ext/styles/css/main.css";
import { DashboardView } from "@gooddata/sdk-ui-ext";
import { idRef } from "@gooddata/sdk-model";

<DashboardView
    dashboard={idRef("<dashboard-id>")}
    transformLayout={(layoutBuilder) => {
        // Modify the first layout section
        layoutBuilder.modifySection(0, (section) =>
            // Add a full-width item to the section
            section.addItem({ gridWidth: 12 }, (item) =>
                // Set custom widget data for the item
                item.widget("Hello world!")
            )
        );

        // Return the layout builder with the transformations applied
        return layoutBuilder;
    }}
    widgetRenderer={({ predicates, customWidget, renderedWidget }) => {
        if (predicates.isCustomWidget()) {
            return (
                // Render the custom widget data
                <div style={{ height: "100%", width: "100%" }}>
                    {customWidget}
                </div>
            );
        }

        // Fallback all other widgets to common rendering
        return renderedWidget;
    }}
/>;
```

TODO: link to advanced customizations

## Integration with your application

DashboardView provides several mechanisms to facilitate its integration into your application.

You can use the `onDashboardLoaded` callback to get all the information about the dashboard object and any KPI Alerts set on it for the given user. This includes also information about any filters set up on the dashboard (so that you can initialize any filter UI you might have accordingly).

You can use the `filters` property to replace the filters of the dashboard so that you can create a custom filters UI for your application.

The `onFiltersChange` callback is called whenever a widget inside the DashboardView requests that the filters be changed (this can happen in case the user opens a KPI Alert which was created using a different filters than those currently used and wishes to use the filters that were active when the KPI Alert was created).

The `onDrill` callback can be used to detect and react to any drill events happening inside the DashboardView.

The `useDashboardPdfExporter` hook can be used to perform an export of a given dashboard to PDF see TODO link to examples (note that this will not reflect any [customizations](#customizations) you may have made).

There is also an example of how to emulate edit mode in [Embed a Dashboard Created in KPI Dashboards](30_tips__embed_dashboard.md#edit-mode).

## Caching

To properly render the referenced dashboards and the visualizations in it, the DashboardView component needs additional information from the GoodData platform. This information is usually static. To minimize the number of redundant requests and reduce the rendering time, some static information (such as the list of visualization classes, the color palette, or feature flags for each project) is cached for all DashboardView components in the same application.

The amount of the cached information does not impact performance in any way. However, you can manually clear the cache whenever needed (for example, after logging out, when switching projects or leaving a page with visualizations using the GoodData.UI components).

```javascript
import { clearDashboardViewCaches } from "@gooddata/sdk-ui-ext";
...
clearDashboardViewCaches();
...
```

## Properties

| Name                         | Required? | Type                                             | Description                                                                                                                                       |
| :--------------------------- | :-------- | :----------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------ |
| dashboard                    | true      | ObjRef or string                                 | Reference to the dashboard to render                                                                                                              |
| filters                      | false     | Array<IDashboardFilter &#124; FilterContextItem> | Filters to use for the dashboard (see [Filters](#filters))                                                                                        |
| onFiltersChange              | false     | Function                                         | Called when the filters should be changed (see [Integration with your application](#integration-with-your-application))                           |
| config                       | false     | IDashboardConfig                                 | Configuration object for the visualizations (see [Configuration](#configuration))                                                                 |
| drillableItems               | false     | [IDrillableItem[]](15_props__drillable_item.md)  | An array of points and attribute values to be drillable                                                                                           |
| onDrill                      | false     | Function                                         | A callback when a drill is triggered on any of the widgets in the dashboard                                                                       |
| theme                        | false     | ITheme                                           | Theme to be used for the dashboard (see [Theming](#theming))                                                                                      |
| disableThemeLoading          | false     | boolean                                          | If `true`, DashboardView will not try to load Theme from the backend                                                                              |
| ErrorComponent               | false     | Component                                        | A component to be rendered if this component (or any of the widgets) is in error state (see [ErrorComponent](15_props__error_component.md))       |
| LoadingComponent             | false     | Component                                        | A component to be rendered if this component (or any of the widgets) is in loading state (see [LoadingComponent](15_props__loading_component.md)) |
| onDashboardLoaded            | false     | Function                                         | Called when the data for the dashboard is loaded (see [Integration with your application](#integration-with-your-application))                    |
| onError                      | false     | Function                                         | Called in case of any error, either in the dashboard loading or any of the widgets execution.                                                     |
| isScheduledMailDialogVisible | false     | boolean                                          | If `true`, dialog for Scheduled emails will be displayed (defaults to `false`)                                                                    |
| applyFiltersToScheduledMail  | false     | boolean                                          | Specifies if Scheduled email should use filters from `filters` prop (see [Scheduled emails](#scheduled-emails))                                   |
| onScheduledMailDialogSubmit  | false     | Function                                         | Called when the user submits Scheduled email dialog                                                                                               |
| onScheduledMailDialogCancel  | false     | Function                                         | Called when the user closes Scheduled email dialog                                                                                                |
| onScheduledMailSubmitSuccess | false     | Function                                         | Called when the Scheduled email was successfully created                                                                                          |
| onScheduledMailSubmitError   | false     | Function                                         | Called when creating of the Scheduled email fails                                                                                                 |
| isReadOnly                   | false     | boolean                                          | Specifies if the [Read-only mode](#read-only-mode) is enabled (defaults to `false`)                                                               |
| widgetRenderer               | false     | Function                                         | Render prop to override rendering of individual widgets (see [Customizations](#customizations))                                                   |
| transformLayout              | false     | Function                                         | Callback to transform the dashboard layout (see [Layout](#layout))                                                                                |
