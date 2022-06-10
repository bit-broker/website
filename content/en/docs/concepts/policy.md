---
title: "Data Sharing Policy"
linkTitle: "Policies"
weight: 5
description: Create, manage and deploy data sharing policies to grant access to data
---

Data sharing policies are the main back-bone of the BitBroker system. They are defined by [coordinators](/docs/concepts/users/#coordinators), who use them to specify the exact context in which they permit data to be accessed by [consumers](/docs/concepts/users/#consumers).

Policy definitions can only by submitted by coordinators via the corresponding end-points in the [Coordinator API](/docs/coordinator/policy/). Once a policy is deployed, [accesses](/docs/concepts/access/) can be [created](/docs/coordinator/access/#creating-a-new-access) which allow consumers to interact with the data specified in the ways allowed.

A policy definition is made up of three sections. We will outline each in detail.

### Data Segment

The _data segment_ section of a policy definition, defines the maximum subset of data which will be visible when users access the [Consumer API](/docs/consumer/) via a policy key. When consumer key a policy key, they are locked inside this data segment. There is no action they can perform with the Consumer API to break out in the wider [catalog](/docs/concepts/catalog/) of data.

A data segment definition is made up of the following attributes:

Attribute | Necessity | Description
--- | --- | ---
`segment_query` | <div class="stamp">required</div> | A valid [catalog query](/docs/consumer/catalog/), from the [Consumer API](/docs/consumer/) <br/> Defines the visible subset of data
`field_masks` | <div class="stamp">optional</div> | An array of strings<br/> Attributes to be masked out of returned documents

Data segments are defined via the same semantics as users searching the [catalog](/docs/concepts/catalog/) using [catalog queries](/docs/consumer/catalog/). You should think of your data segment as a second query which will be boolean `and` with the user's queries.

{{% alert color="info" %}}
If a consumer asks for entity instance data which is out-of-policy, they will be returned an `HTTP/1.1 404 Not Found` error. The system will deny the existence of such entities. If two consumers share a BitBroker link, where they have different policies - it is possible that one may see an data document and other a `HTTP/1.1 404 Not Found` for the same requesting API end-point.
{{% /alert %}}

Field masks are a way of removing individual attributes from [entity instance](/docs/concepts/entity-types/#entity-instances) data records. You can only remove attributes from within the `entity` section of the overall document. You do this by specify an array of strings, where each one is `[entity-type].[attribute]`.

For example, if you specify `[ "country.capital", "country.currency.name", "country.location" ]` then three attributes will be removed from the `entity` section of all returned documents. So this record:

```js
{
    "id": "GB",
    "name": "United Kingdom",
    "entity": {
        "area": 242900,
        "calling_code": 44,
        "code": "GB",
        "continent": "Europe",
        "currency": {
            "code": "GBP"
        },
        "landlocked": false,
        "location": {
            "coordinates": [
                -3.435973,
                55.378051
            ],
            "type": "Point"
        },
        "population": 66040229,
        "link": "https://en.wikipedia.org/wiki/United_Kingdom"
    }
}
```

will be returned like this, for in-policy calls:

```js
{
    "id": "GB",
    "name": "United Kingdom",
    "entity": {
        "area": 242900,
        "calling_code": 44,
        "capital": "London",
        "code": "GB",
        "continent": "Europe",
        "currency": {
            "code": "GBP",
            "name": "Sterling"
        },
        "landlocked": false,
        "population": 66040229,
        "link": "https://en.wikipedia.org/wiki/United_Kingdom"
    }
}
```

### Access Control

The _access control_ section of a policy definition, defines the ways in which users can interact with the [Consumer API](/docs/consumer/) via a policy key. An access control definition is made up of the following attributes:

Attribute | Necessity | Description
--- | --- | ---
`enabled` | <div class="stamp">required</div> | Whether or note to use access control
`quota` | <div class="stamp">optional</div> | An object describing allowable data quotas
`quota.max_number` | <div class="stamp">optional</div> | A number of calls for the quota
`quota.interval_type` | <div class="stamp">optional</div> | The quota period of either 'day' or 'month'
`rate` | <div class="stamp">optional</div> | The maximum calls-per-second rate

If you do not want to use the access control section, you can simply specify `false` for the `enabled` attribute. In this case, all other attributes will be ignored and the consumer will enjoy unrestricted access rates to the [Consumer API](/docs/consumer/).

As an example, if you specify the following:

```js
{
    "enabled": true,
    "quota": {
        "max_number": 86400,
        "interval_type": "day"
    },
    "rate": 250
}
```

Users with a policy key will be able to make calls at a maximum rate of 250-per-second and with a maximum quota of 86,400-per-day.

{{% alert color="info" %}}
If a consumer breaches a rate or quota limit, then calls to any part of the [Consumer API](/docs/consumer/) will respond `HTTP/1.1 429 Too Many Requests` error. This response will persist until the breach has expired.
{{% /alert %}}

### Legal Context

The _legal context_ section of a policy definition, defines the legal basis on which data access is permitted. A legal notice will be present at the end of every entity instance record returned by any part of the [Consumer API](/docs/consumer/).

These legal notices are in the form of a JSON array of objects, each with three attributes:

Attribute | Necessity | Description
--- | --- | ---
`type` | <div class="stamp">required</div> | The type of the legal notice
`text` | <div class="stamp">required</div> | The description of the legal notice
`link` | <div class="stamp">required</div> | A link for more information about the legal notice

Examples of types of legal notices which may be present are:

Type | Description
--- | ---
`attribution` | How the data should be attributed within your application
`contact` | Who to contact about the data and it's use
`license` | The licenses under which this data sharing operates
`note` | Ad hoc notes about the data and/or it's use
`source` | Information about the source or origination of the data
`terms` | The terms and conditions of use for the data

It is possible that you may have more information about the legal basis on the use of your data by consumers. You may, for example, require consumers to perform additional legal steps in order to be given an consumer access key. This is outside of the current scope of BitBroker.
