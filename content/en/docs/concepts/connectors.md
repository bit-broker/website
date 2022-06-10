---
title: "Data Connectors"
linkTitle: "Connectors"
weight: 3
description: Data connectors which for allow contribution of data into a BitBroker instance
---

BitBroker is a contribution based system, meaning that data contributed by a community of [users](/concepts/users/#contributors). In some cases, these contributors will be people you have direct control over (and may well be other roles you yourself are playing). However, in other instances, you maybe relying upon second and third parties to be contributing data into your BitBroker instance.

The vector through which data contribution is managed is the concept of data connectors. Each [entity type](/docs/concepts/entity-types/) within the system should have at least one data connector. However, it is entirely possible for an entity type to be sharing multiple data connectors, all contributing [entity instance records](/docs/concepts/entity-types/#entity-instances) for that type.

A data connector can only be contributing data to _one_ entity type. If a party wants to contribute data to multiple entity types, you must create a different connector for each one. These can, in practice, point back to one data connector implementation.

{{% alert color="primary" %}}
A quick way to get going building data connectors is to adapt the [example connectors](/docs/examples/connectors/) which have been built for a range of data sources.
{{% /alert %}}

#### Managing Data Contribution

Whether or not the contribution is coming from internal or external players, the first step in managing the process is to [create a data connector](/docs/coordinator/connectors/#creating-a-new-connector) - housed within the [entity type](/docs/concepts/entity-types/) for which it has permission to submit records. Creating and managing data connectors can only be done by [coordinator users](/docs/concepts/users/#coordinators).

As part of the creation process for connectors, the system will generate a connector ID and an access key. These will be returned to the coordinator user in response to the creation request. The coordinator should communicate these items in a secure manner to the party responsible for implementing the data connector. Here is an example of these items:

```js
{
    "id":"9afcf3235500836c6fcd9e82110dbc05ffbb734b",
    "token":"ef6ba361-ef55-4a7a-ae48-b4ecef9dabb5.5ab6b7a2-6a50-4d0a-a6b4-f43dd6fe12d9.7777df3f-e26b-4f4e-8c80-628f915871b4"
}
```

Armed with these two items, data connectors can go ahead and contribute [entity instance records](/docs/concepts/entity-types/#entity-instances) into [the catalog](/docs/concepts/catalog/), by using the [Contributor API](/docs/contributor/). You should direct new data connectors to the [contributor documentation](/docs/contributor/records/) in order get them started with their implementation.

{{% alert color="info" %}}
Connector authors should be aware of the important distinction between _data_ and _metadata_ within the context of the BitBroker [catalog](/docs/concepts/catalog/).
{{% /alert %}}

{{% alert color="info" %}}
Third parties can implement their data connectors in whatever manner they want. The system only requires that they communicate their submissions via the HTTP based mechanisms outlined in the [Contributor API](/docs/contributor/). Coordinator users may well have their own requirements which they impose upon contributors. However, this is of no direct concern to BitBroker.
{{% /alert %}}

{{% alert color="info" %}}
Because of the way the division of responsibility has been separated within the system, BitBroker has no requirements to know or understand how the original source data is stored or secured. This is purely the concern of the data connector authors.
{{% /alert %}}

{{% alert color="info" %}}
Contribution is scoped to be _within_ the connector's domain space only. A connector cannot affect records delivered by another connector, even within the same entity type and even if they have a clashing key space.
{{% /alert %}}

{{% alert color="info" %}}
Coordinators can use the mechanism of [entity schemas](/docs/concepts/entity-types/#entity-schemas) to ensure alignment around things such as data types, formats, representation schemes and semantics.
{{% /alert %}}

##### Live vs Staging Connectors

By default, newly created connectors are not "live". This means that data which they contribute will _not_ be visible within the [Consumer API](/docs/consumer/). Implementing a data connector can be a tricky operation and may require a few attempts before data is being delivered to a sufficient quality. Hence it is useful to isolate contributions from certain connectors into a "staging space" - so that it doesn't pollute the public space visible to [consumers](/docs/concepts/users/#consumers).

In order to make a connector's data visible, it must be [promoted to live status](/docs/coordinator/connectors/#promoting-a-connector-to-live). Only [coordinator](/docs/concepts/users/#coordinators) users can promote connectors in this way. They can also [demote connectors](/docs/coordinator/connectors/#demoting-a-connector-from-live), if they believe their data should no longer be publically visible.

When developing a new connector, it is often useful to be able to see data from it alongside other public data. There is a [mechanism available](/docs/consumer/#accessing-staged-records) in the [Consumer API](/docs/consumer/) which allows data connectors to see how their records will look alongside other existing public records.
