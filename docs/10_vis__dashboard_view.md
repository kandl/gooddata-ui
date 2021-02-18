---
title: DashboardView
sidebar_label: DashboardView
copyright: (C) 2007-2018 GoodData Corporation
id: dashboard_view_component
---

The **DashboardView component** is a generic component that renders dashboards created and saved by KPI Dashboards.

## Structure

```jsx
import "@gooddata/sdk-ui-ext/styles/css/main.css";
import { DashboardView } from "@gooddata/sdk-ui-ext";

<div style={{ height: 400, width: 600 }}>
    <DashboardView dashboard="<visualization-identifier>" />
</div>;
```

```jsx
import "@gooddata/sdk-ui-ext/styles/css/main.css";
import { DashboardView } from "@gooddata/sdk-ui-ext";
import { uriRef } from "@gooddata/sdk-model";

<div style={{ height: 400, width: 600 }}>
    <DashboardView dashboard={uriRef("<visualization-uri>")} />
</div>;
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

**NOTE**: There is currently a limitation on the attribute filters: you can only use URIs to identify the attribute elements.

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

TODO help permalink

You can allow users to create new [Scheduled emails](https://help.gooddata.com/doc/en/reporting-and-dashboards/kpi-dashboards/schedule-automatic-emailing-of-kpi-dashboards) for the dashboard. You can display the dialog for setting up the Scheduled email by setting the `isScheduledMailDialogVisible` to true. There are also other props you can use to interact with the dialog:

-   `onScheduledMailDialogSubmit` – called when the user confirms the Scheduled email creation. The callback will receive the Scheduled email definition the user submitted as a parameter.
-   `onScheduledMailDialogCancel` – called when the user cancels or closes the Scheduled email dialog
-   `onScheduledMailSubmitSuccess` – called when the Scheduled email is created and stored on the server. The callback will receive the saved Scheduled email definition as a parameter.
-   `onScheduledMailSubmitError` – called when the Scheduled email creation fails. The callback will receive the error object as a parameter.

By default, the Scheduled emails created this way will be filtered by the filters passed in the `filters` prop if specified (see [Filters](#filters)) or fall back to the filters set up on the dashboard in KPI Dashboards. If you would prefer the Scheduled email to always use the filters that were set on the dashboard in KPI Dashboards, set the `applyFiltersToScheduledMail` to `false` (defaults to `true`).

## KPI Alerts

TODO help permalink

DashboardView supports displaying, creating, editing and deleting of [KPI Alerts](https://help.gooddata.com/doc/en/reporting-and-dashboards/kpi-dashboards/add-an-alert-to-a-kpi).
The Alerts will use the filters in the `filters` prop if provided (see [Filters](#filters)), or fall back to the filters set up on the dashboard in KPI Dashboards.

## Read-only mode

By default, DashboardView will allow users with appropriate permissions to create KPI Alerts and Scheduled emails. If you do not want this, you can set the `isReadonly` prop to `true` (defaults to `false`). This will completely disable the KPI Alerts adn Scheduled emails features.

## Customizations

## Integration with your application

DashboardView provides several mechanisms to facilitate its integration into your application.

You can use the `onDashboardLoaded` callback to get all the information about the dashboard object and any KPI Alerts set on it for the given user. This includes also information about any filters set up on the dashboard (so that you can initialize any filter UI you might have accordingly).

The `onFiltersChange` callback is called whenever a widget inside the DashboardView requests that the filters be changed (this can happen in case the user opens a KPI Alert which was created using a different filters than those currently used and wishes to use the filters that were active when the KPI Alert was created).

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

TODO links to IDashboardFilter and FilterContextItem

| Name                         | Required? | Type                                             | Description                                                                                                                                       |
| :--------------------------- | :-------- | :----------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------ |
| dashboard                    | true      | ObjRef or string                                 | Reference to the dashboard to render                                                                                                              |
| filters                      | false     | Array<IDashboardFilter &#124; FilterContextItem> | Filters to use for the dashboard                                                                                                                  |
| onFiltersChange              | false     | Function                                         | Called when the filters should be changed (see [Integration with your application](#integration-with-your-application))                           |
| config                       | false     | IDashboardConfig                                 | Configuration object for the visualizations (see [Configuration](#configuration))                                                                 |
| drillableItems               | false     | [IDrillableItem[]](15_props__drillable_item.md)  | An array of points and attribute values to be drillable                                                                                           |
| onDrill                      | false     | Function                                         | A callback when a drill is triggered on any of the widgets in the dashboard                                                                       |
| theme                        | false     | ITheme                                           | Theme to be used for the dashboard (see [Theming](#theming))                                                                                      |
| disableThemeLoading          | false     | boolean                                          | If `true`, DashboardView will not try to load Theme from the backend                                                                              |
| ErrorComponent               | false     | Component                                        | A component to be rendered if this component (or any of the widgets) is in error state (see [ErrorComponent](15_props__error_component.md))       |
| LoadingComponent             | false     | Component                                        | A component to be rendered if this component (or any of the widgets) is in loading state (see [LoadingComponent](15_props__loading_component.md)) |
| onDashboardLoaded            | false     | Function                                         | Called when the data for the dashboard is loaded (see [Integration with your application](#integration-with-your-application)                     |
| onError                      | false     | Function                                         | Called in case of any error, either in the dashboard loading or any of the widgets execution.                                                     |
| isScheduledMailDialogVisible | false     | boolean                                          | If `true`, dialog for Scheduled emails will be displayed (defaults to `false`)                                                                    |
| applyFiltersToScheduledMail  | false     | boolean                                          | Specifies if Scheduled email should use filters from `filters` prop (see [Scheduled emails](#scheduled-emails))                                   |
| onScheduledMailDialogSubmit  | false     | Function                                         | Called when the user submits Scheduled email dialog                                                                                               |
| onScheduledMailDialogCancel  | false     | Function                                         | Called when the user closes Scheduled email dialog                                                                                                |
| onScheduledMailSubmitSuccess | false     | Function                                         | Called when the Scheduled email was successfully created                                                                                          |
| onScheduledMailSubmitError   | false     | Function                                         | Called when creating of the Scheduled email fails                                                                                                 |
| isReadOnly                   | false     | boolean                                          | Specifies if the [Read-only mode](#read-only-mode) is enabled                                                                                     |
| TODO customization props     |           |                                                  |                                                                                                                                                   |
