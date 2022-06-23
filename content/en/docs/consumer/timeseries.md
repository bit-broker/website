---
title: "Time Series API"
linkTitle: "Time Series API"
weight: 3
description: The time series API enabling access to time-value pair datasets within entity instances
---

The Timeseries API is used to retrieve timeseries data points from entity types present within [the catalog](/docs/concepts/catalog/). Not all entity types have timeseries associated with them.

In this section, we will explore the capabilities of the Timeseries API in depth.

{{% alert color="primary" %}}
A quick way to get going with building your own applications is to adapt the [example apps](/docs/examples/applications/) which use this and the other [Consumer APIs](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
All Consumer API calls happen within the context of a [data sharing policy](/docs/concepts/policy/). The policy defines a data segment which you are allowed to access. Any queries which you make using the Timeseries API can only operate within the data segment you are permitted access to.
{{% /alert %}}

{{% alert color="primary" %}}
In our sample calls, we use the standard [server name and port](/docs/getting-started/install-local/#server-naming-and-ports) format outlined for [local installations](/docs/getting-started/install-local/) and [demos](/docs/getting-started/demo/). If you have an alternative API host base URL, then enter it in the box below to update all the sample calls on this page :<br/><br/>_Your API Host Base URL_<br/><input class="code-replace" data-item="http://bbk-consumer:8003" data-name="base url" type="text" placeholder="enter url here">
{{% /alert %}}

{{% alert color="primary" %}}
The Consumer API can be run in production _or_ development mode; each requiring a different header. If you are running a [local installation](/docs/getting-started/install-local/) of BitBroker, then you can switch the sample calls on this page to development mode by activating the toggle below. If you are unsure, leave this toggle off.<br/>
<div class="toggle">
  <label class="switch">
    <input class="header" type="checkbox">  
    <span class="slider">
      <span>Apply development mode</span>
    </span>
  </label>
</div>
{{% /alert %}}

{{% alert id="mode-prd" color="primary" %}}
All API calls in BitBroker require [authorization](/docs/api-conventions/authorization/). The sample calls below contain a placeholder string where you should insert your [consumer API authorization token](/docs/api-conventions/authorization/#obtaining-a-consumer-token). This authorization token should have been provided to you by the coordinator user who administers the BitBroker instance. If you already have a token, enter it in the box below to update all the sample calls on this page:<br/><br/>_Your Consumer API Authorization Token_<br/><input class="code-replace" data-item="your-header-goes-here" data-name="header" type="text" placeholder="enter token here">
{{% /alert %}}

{{% alert id="mode-dev" color="primary" %}}
When installing BitBroker locally, [authorization](/docs/api-conventions/authorization/) is bypassed. In this scenario, you need to specify the policy you are using in a [development header](/docs/getting-started/install-local/#development-only-headers). Enter your desired policy in the box below to update all the sample calls on this page:<br/><br/>_Your Current Policy_<br/><input class="code-replace" data-item="your-header-goes-here" data-name="header" type="text" placeholder="enter policy id here" value="access-all-areas">
{{% /alert %}}

### Getting Timeseries Data

You can query for a list timeseries data by issuing an `HTTP/GET` to the `/entity/:type/:id/timeseries/:tsid` end-point.

Timeseries are always housed within a parent entity type and each has a unique ID on that entity type. Hence, you will need to know the entity type ID (`type`), the entity instance ID (`id`) and timeseries ID (`tsid`), in order to get access to such data points.

```shell
curl http://bbk-consumer:8003/v1/entity/country/34c3ab32774042098ddc0ffa9878e4a1a60b33c0/timeseries/population \
     --header "x-bbk-auth-token: your-header-goes-here"
```

This will return a JSON array as follows:

```js
[
    {
        "from": 1960,
        "value": 89608
    },
    {
        "from": 1961,
        "value": 97727
    },
    {
        "from": 1962,
        "value": 108774
    },
    {
        "from": 1963,
        "value": 121574
    },
    // other timeseries data points here
]
```

In this response, the attribute you will see is as follows:

Attribute | Presence | Description
--- | --- | ---
`from` | <div class="stamp">always</div> | An [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`to` | <div class="stamp">sometimes</div> | When present, an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`value` | <div class="stamp">always</div> | A valid JSON data type or object

The exact nature of the `value` attribute will depend upon the context of the timeseries you are using. It might be as simple as an integer, or as complex as an object.

{{% alert color="info" %}}
The timeseries will always be returned sorted on the `from` attribute. Taking the first item of a returned array, should always represent the latest data point.
{{% /alert %}}

#### Paging Timeseries

It is possible that timeseries may run to a great many data points. There is a hard limit to how many data points will be returned in a single call to this API.

{{% alert color="warning" %}}
The hard limit to how many data points will be returned in a single call to this API is __500__
{{% /alert %}}

It is possible to page access to timeseries data points by using a set of URL query parameters, as follows:

Attribute | Necessity | Description
--- | --- | ---
`start` | <div class="stamp">optional</div> | An [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`end` | <div class="stamp">optional</div> | An [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`duration` | <div class="stamp">optional</div> | An integer number of seconds, greater than 0
`limit` | <div class="stamp">optional</div> | An integer number of data points, between 1 and 500

Here are additional considerations for when using these paging parameters:

* If you specify and `end`, you must also specify a `start`
* If you specify and `end`, it must be after the `start`
* If you specify and `duration`, you must also specify a `start`
* You cannot specify an `end` and a `duration`

If the paging parameters are incorrect, the API will respond with a [standard validation error](/docs/api-conventions/errors/#validation-error-format) containing details of the violation.
