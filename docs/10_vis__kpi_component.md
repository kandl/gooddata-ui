---
title: KPI
sidebar_label: KPI
copyright: (C) 2007-2018 GoodData Corporation
id: kpi_component
---

A **KPI** \(Key Performance Indicator\) renders a measure calculated by the GoodData platform.

## Structure

```jsx
import { Kpi } from "@gooddata/react-components";

<Kpi
    measure="<measure-identifier>"
    filters={<filters>}
    format="<format>"
    separators={<separators>}
/>
```

## Example

The following sample KPI calculates the total sales in California:

```jsx
import { Kpi } from "@gooddata/sdk-ui";
import { newPositiveAttributeFilter } from "@gooddata/sdk-model";
import { Ldm } from "./ldm";

<Kpi
    measure={Ldm.$TotalSales}
    filters={[
        newPositiveAttributeFilter(Ldm.StateName, ["California"])
    ]}
/>
```

## Properties

| Name | Required? | Type | Description |
| :--- | :--- | :--- | :--- |
| measure | true | string | The measure URI |
| filters | false | [FilterItem[]](30_tips__filter_visual_components.md) | An array of filter definitions |
| format | false | string | The measure format. If specified, overrides the format stored with the measure. |
| separators | false | object | The format of the thousands separator and decimal separator used in measures. The default is `{ thousand: ",", decimal: "." }`. For more information about setting the regional number format in a GoodData account, see [Set Default Number Format for your User Account](https://help.gooddata.com/pages/viewpage.action?pageId=34340999). |
| onError | false | function | Custom error handler. Called with the argument containing the state and original error message, for example, `{ status:ErrorStates.BAD_REQUEST,error: {...} }`. See the [full list of error states](https://github.com/gooddata/gooddata-ui-sdk/blob/master/libs/sdk-ui/src/base/errors/GoodDataSdkError.ts). Defaults to `console.error`. |
| onLoadingChanged | false | function | Custom loading handler. Called when a KPI changes to/from the loading state. Called with the argument denoting a valid state, for example, `{ isLoading:false}`. |
