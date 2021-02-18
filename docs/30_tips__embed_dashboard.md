---
title: Embed a Dashboard Created in KPI Dashboards
sidebar_label: Embed a Dashboard Created in KPI Dashboards
copyright: (C) 2007-2021 GoodData Corporation
id: embed_dashboard
---

To embed an existing dashboard created in KPI Dashboards, use the [DashboardView component](10_vis__insight_view.md).

**Steps:**

1. Obtain the identifier of the dashboard via [catalog-export](02_start__catalog_export.md).

2. Import the DashboardView component from the `@gooddata/sdk-ui-ext` package into your app:

    ```javascript
    import { DashboardView } from "@gooddata/sdk-ui-ext";
    ```

3. Create an `DashboardView` component in your app, and provide it with the project ID and the visualization identifier that you obtained at Step 1:

    ```jsx
    import { DashboardView } from "@gooddata/sdk-ui-ext";
    import "@gooddata/sdk-ui-ext/styles/css/main.css";

    <DashboardView insight="aby3polcaFxy" />;
    ```
