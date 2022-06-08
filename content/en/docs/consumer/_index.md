---
title: "Consumer API"
linkTitle: "Consumer API"
weight: 7
description: The outward facing API which consumers will use to access data from BitBroker
---

The Consumer API is the main outward facing API for the BitBroker system. It is the API used by [consumers](/docs/concepts/users/#consumers) to access data from the system, but always in the context of a [data sharing policy](/docs/concepts/policy/).

{{% alert color="info" %}}
All Consumer API calls happen in the context of a [data sharing policy](/docs/concepts/policy/). The policy defines a data segment which you are permitted access to. There are no operations within the Consumer API which allow you to "break out" of this defined data segment.
{{% /alert %}}

{{% alert color="primary" %}}
Before you use this API, you should become familiar with the general, system-wide [API principles](/docs/api-principles/) - which are used across all three BitBroker API sets. This covers topics such as API structures, error reporting and handling, authorisation, server names and ports, etc.
{{% /alert %}}

#### Rate and Quota Limits

All Consumer API calls happen in the context of a [data sharing policy](/docs/concepts/policy/). Amongst other things, policy defines a _rate and quota limit_ on calls you can make with the Consumer API. These should have been communicated to you by the coordinator user who gave you your consumer access key.

* Rate - the maximum calls-per-second that you are allowed to make (e.g. 250)
* Quota - the total calls you can make in a given period (e.g. 86,400 per day)

If you breach a rate or quota limit then calls to any part of the Consumer API will respond as follows:

```
HTTP/1.1 429 Too Many Requests
```

This response will persist until the breach has expired.

#### Legal Notices

All Consumer API calls happen in the context of a [data sharing policy](/docs/concepts/policy/). Amongst other things, policy defines the _legal context_ under which data access is permitted. A legal notice will be present at the end of every entity instance record returned by any part of the Consumer API.

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
