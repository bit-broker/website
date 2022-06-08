---
title: "Hosting a Webhooks"
linkTitle: "Webhooks"
weight: 2
description: How to use webhooks to incorporate live and on-demand data
---

It is an expectation that the BitBroker catalog contains information which is useful to enable search and discovery of entity instances. Hence it contains key metadata - but it does not normally contain actual entity data. This is pulled on-demand via a webhook hosted by the data connector who contributed the entity record. The distinction between data and metadata [is covered in more detail](todo) in the [key concepts](/docs/concepts/) documentation.

In this section we will outline how to implement a webhook within a data container.

{{% alert color="primary" %}}
A quick way to get going integrating a webhook into your own data connector is to adapt the [example connectors](/docs/examples/connectors/) which have been built for a range of data sources.
{{% /alert %}}

{{% alert color="info" %}}
It is permitted and valid for one webhook to be servicing the needs of multiple data connectors. Sufficient inbound information will be provided to allow the webhook to be clear about which entity instance data is being request.
{{% /alert %}}


## Registering your Webhook

The first step is to register your webhook with BitBroker. This is done when the [connector is created](/docs/coordinator/connectors/#creating-a-new-connector) or can be done later by [updating the connector](/docs/coordinator/connectors/#updating-a-connector). These actions are part of the [Coordinator API](/docs/coordinator/) and hence can only be performed by a [coordinator user](/docs/concepts/users/#coordinators) on your behalf.

Your webhook should be an HTTP server which is capable of receiving calls from the BitBroker instance. You can host this server in any manner you like, however the coordinator of your BitBroker may have their own hosting and security requirements of it.

You need to maintain your webhook so that it is always available to its connected BitBroker instance. If you webhook is down or inaccessible when BitBroker needs it, this will result in a poor experience for [consumers](/docs/concepts/users/#consumers) using the [Consumer API](/docs/consumer/). In this scenario, they will only see partial records. Information about misbehaving data connectors will be available to coordinator users.

## Required End-points

You are required to implement _two_ end-points as part of your webhook deployment.

{{% alert color="primary" %}}
Whilst BitBroker advertises its own key space to its [consumers](/docs/concepts/users/#consumers) , there is no need for data connectors to take heed of these. They can continue to concern themselves with only their own domain key space. When BitBroker makes requests of your webhook, it will only ever use own key space.
{{% /alert %}}

{{% alert color="warning" %}}
Whenever your webhook is called, it will be in the context of an _on-demand request_ - meaning that the call is in the direct line of response to a waiting user of the [Consumer API](/docs/consumer/). Hence, you should endeavour to respond to webhook calls in a timely manner. Information about poorly performing data connectors will be available to coordinator users.
{{% /alert %}}

### Entity End-point

The entity end-point is used by BitBroker to get a full data record for an entity instance which you previously submitted into the catalog.

The entity end-point has the following signature:

```
   HTTP/GET /entity/:type/:id
```

Where:

Attribute | Presence | Description
--- | --- | ---
`type` | <div class="stamp">always</div> | The [entity type](/docs/concepts/entity-types/) ID, for this entity instance
`id` | <div class="stamp">always</div> | Your own domain key, which you previously submitted into the catalog

The entity type is presented here to allow for scenarios where one webhook is servicing the needs of multiple data connectors.

In response to this call, you should return a JSON object consisting of an `entity` and `instance` attribute _only_ - all other attributes will be ignored. The object you return will be _merged_ with the catalog record, which you provided earlier. Hence, there is no need to resupply the catalog information you have already submitted in previous steps.

For example, consider this (previously submitted) catalog record:

```js
{
    "id": "GB",
    "name": "United Kingdom",
    "type": "country",  
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
        "population": 66040229
    },
    "instance": {
        "independence": 1066
    }
}
```

If their is a call for the detail of this record made on the [Consumer API](/docs/consumer/), the system will callback on the enitiy end-point as follows:

```
HTTP/GET /entity/country/GB
```

Then the webhook should respond with any extra / live / on-demand `entity` and `instance` data:

```js
{
    "entity": {
        "inflation": 4.3
    },
    "instance": {
        "temperature": 18.8
    }
}
```

The system will then merge this live information with the catalog record to send a combined record to the consumer.


```js
{
    "id": "GB",
    "name": "United Kingdom",
    "type": "country",
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
        "population": 66040229,
        "inflation": 4.3  // this has been merged in
    },
    "instance": {
        "independence": 1066,
        "temperature": 18.8  // this has been merged in
    }
}
```

### Timeseries End-point

The timeseries end-point is used by BitBroker to get a timeseries information associated with an entity instance previously submitted into the catalog.

The timeseries end-point has the following signature:

```
HTTP/GET /timeseries/:type/:id/:tsid?start=:start&end=:end&limit=:limit
```

Where:

Attribute | Presence | Description
--- | --- | ---
`type` | <div class="stamp">always</div> | The [entity type](/docs/concepts/entity-types/) ID, for this entity instance
`id` | <div class="stamp">always</div> | Your own domain key, which you previously submitted into the catalog
`tsid` | <div class="stamp">always</div> | The ID of the timeseries associated with this entity instance
`start` | <div class="stamp">sometimes</div> | The earliest timeseries data point being requested<br/>When present, an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`end` | <div class="stamp">sometimes</div> | The latest timeseries data point being requested<br/>When present, an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`limit` | <div class="stamp">always</div> | The maximum number of timeseries points to return<br/>An integer greater than zero

Further information about the possible URL parameters supplied with this callback are:

Attribute | Information
--- | ---
`start` | Should be treated as _inclusive_ of the range being requested<br/>When not supplied, assume a `start` from the _latest_ timeseries point
`end` | Should be treated as _exclusive_ of the range being requested<br/>When present, this will always after the `start`<br/>Never present without `start` also being present<br/>When not supplied, defer to the `limit` count
`limit` | Takes prescedence over the `start` and `end` range<br/>The `end` may not be reached, if `limit` is breached first

Then the webhook should respond timeseries data points as follows:

```js
[
    {
        "from": 1910,
        "to": 1911,
        "value": 5231
    },
    {
        "from": 1911,
        "to": 1912,
        "value": 6253
    },
    // other timeseries points here
]
```

Where:

Attribute | Necessity | Description
--- | --- | ---
`from` | <div class="stamp">required</div> | An [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`to` | <div class="stamp">optional</div> | When present, an [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted date
`value` | <div class="stamp">required</div> | A valid JSON data type or object

{{% alert color="info" %}}
You should return your timeseries points with the latest first. Taking the first item of an returned array, should always represent the latest data point.
{{% /alert %}}

Specifying both `from` and `to` is rare - in most cases, only a `from` will be present. You can place any data type which makes sense for your timeseries in the `value` attribute. But this should be consistent across all the timeseries points you return.
