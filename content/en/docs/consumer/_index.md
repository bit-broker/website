---
title: "Consumer API"
linkTitle: "Consumer API"
weight: 6
description: The outward facing API which consumers will use to access data from BitBroker
---

The Consumer API is the main outward facing API for the BitBroker system. It is the API used by [consumers](/docs/concepts/users/#consumers) to access data from the system, but always in the context of a [data sharing policy](/docs/concepts/policy/).

{{% alert color="info" %}}
All Consumer API calls happen in the context of a [data sharing policy](/docs/concepts/policy/). The policy defines a data segment which you are permitted access to. There are no operations within the Consumer API which allow you to "break out" of this defined data segment.
{{% /alert %}}

{{% alert color="primary" %}}
Before you use this API, you should become familiar with the general, system-wide [API conventions](/docs/api-conventions/) - which are used across all three BitBroker API sets. This covers topics such as API architecture, authorisation, error reporting and handling, etc.
{{% /alert %}}

#### Paging Lists

It is possible that record lists returned from APIs may run to a great many records. There is a hard limit to how many records will be returned in a single call to any Consumer API.

{{% alert color="warning" %}}
The hard limit to how many records will be returned in a single call to the Consumer API is __250__
{{% /alert %}}

It is possible to page access to record lists by using a set of URL query parameters, as follows:

Attribute | Necessity | Description
--- | --- | ---
`limit` | <div class="stamp">optional</div> | An integer number of data records, between 1 and 250
`offset` | <div class="stamp">optional</div> | A positive integer index of the earlier desired record

If the paging parameters are incorrect, the API will respond with a [standard validation error](/docs/api-conventions/errors/#validation-error-format) containing details of the violation.

If the specified `offset` is greater than the count of records available, the API will return an empty list. Using `limit` and `offset` you can page your way through long list, getting the entire list a page at a time.

{{% alert color="info" %}}
Timeseries data has [separate paging semantics](/docs/consumer/timeseries/#paging-timeseries), which is more attuned to it's time-value pair data points.
{{% /alert %}}

#### Rate and Quota Limits

All Consumer API calls happen within the context of a [data sharing policy](/docs/concepts/policy/). Amongst other things, policy defines a _rate and quota limit_ on calls you can make with the Consumer API. These should have been communicated to you by the coordinator user who gave you your consumer access key.

* Rate - the maximum calls-per-second that you are allowed to make (e.g. 250)
* Quota - the total calls you can make in a given period (e.g. 86,400 per day)

If you breach a rate or quota limit then calls to any part of the Consumer API will respond as follows:

```
HTTP/1.1 429 Too Many Requests
```

This response will persist until the breach has expired.

#### Legal Notices

All Consumer API calls happen within the context of a [data sharing policy](/docs/concepts/policy/). Amongst other things, policy defines the _legal context_ under which data access is permitted. A legal notice will be present at the end of every entity instance record returned by any part of the Consumer API.

These legal notices are in the form of a JSON array of objects, each with three attributes:

Attribute | Presence | Description
--- | --- | ---
`type` | <div class="stamp">always</div> | The type of the legal notice
`text` | <div class="stamp">always</div> | The description of the legal notice
`link` | <div class="stamp">always</div> | A link for more information about the legal notice

Examples of types of legal notices which may be present are:

Type | Description
--- | ---
`attribution` | How the data should be attributed within your application
`contact` | Who to contact about the data and it's use
`license` | The licenses under which this data sharing operates
`note` | Ad hoc notes about the data and/or it's use
`source` | Information about the source or origination of the data
`terms` | The terms and conditions of use for the data

{{% alert color="warning" %}}
Please respect the legal basis under which the data is being shared. Coordinator users have the power to rescind consumer access keys and hence cut-off access to data for users who breach the legal context.
{{% /alert %}}

It is possible that the coordinator user who gave you your consumer access key will have more information about the legal basis of use of the data. They may require you to perform additional legal steps in order to be given an consumer access key.

#### Accessing "Staged" Records

It is important to understand that [data connectors](/docs/concepts/connectors/) might be in a _live_ or _staged_ state. That is their contribution might be approved for the live catalog, or might be being held back into a staging space only.

When staged, any records they contributed will not be visible in any part of the Consumer API. This concept is [outlined in more detail](/docs/concepts/connectors/#live-vs-staging-connectors) in the key concepts documentation.

When developing a new connector, it is often useful to be able to see data from it alongside other public data. There is a mechanism available in the Consumer API which allows data connectors to see how their records will look alongside other existing public records.

In order to achieve this, connectors authors can send in a list of connector IDs in a private HTTP header attribute to any part of the Consumer API. Only connector authors will be aware of their connector ids. They can send up to 16 connectors in a single request header. The API will then include in responses, records contributed by those connectors _as if_ they were live.

The header they should use for this is `x-bbk-connectors`. This should be a string value which is a comma separated list of connector IDs without any spaces.
