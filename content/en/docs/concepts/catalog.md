---
title: "The Catalog"
linkTitle: "Catalog"
weight: 5
description: The catalog and how it facilitates search, discovery and access
---

{{% alert color="primary" %}}
Read the corresponding API documentation [here](/docs/consumer/catalog/)
{{% /alert %}}

The BitBroker catalog is a place where the existence of all the [entity instances](/docs/concepts/entity-types/#entity-instances) in the domain space are stored, enumerable and searchable. It also provides the route for [consumers](/docs/concepts/users/#consumers) to search, discover and use entity instance data.

The BitBroker catalog performs the following specific functions:

1. Records the existence of all domain [entity instances](/docs/concepts/entity-types/#entity-instances)
1. Marshalls all domain data into a published [entity type](/docs/concepts/entity-types/) enumeration
1. Presents a small set of [global attributes](/docs/contributor/records/#global-attributes) in a consistent manner
1. Ensures data representation consistency between [entity specific properties](/docs/contributor/records/#entity-attributes)
1. Arbitrates the key spaces of the domain systems managed by [data connectors](/docs/concepts/connectors/)
1. Offers consistent listing, enumeration and search semantics over all entries via the [Consumer API](/docs/consumer/)
1. Acts as the bridge to the connected domain systems
1. Removes the need for users to understand where the source data originated from
1. Blends data from multiple [data connectors](/docs/concepts/connectors/) for the same [entity type](/docs/concepts/entity-types/)
1. Provides key arbitration between multiple data connectors
1. Maximises data interoperability

Consumer access to the the catalog is via [its own API](/docs/consumer/catalog/) which is a part of the [Consumer API](/docs/consumer/).

#### Data vs Metadata

The catalog is a place to store _metadata_ about entity instances. Consumer use the catalog to search and discover entity instances which are needed for their applications. Once an entity instance has been discovered, its details can be obtained via calls the [relevant part](/docs/consumer/entity/) of the Consumer API.

When consumers obtain a detailed record about a particular entity instance, BitBroker will ask it's submitting data connector directly for it's live and on-demand information via a [webhook](/docs/contributor/webhooks/). It will then merge this with the catalog record and return the whole to the consumer. The consumer is unaware of the existence of data connectors and even that some data is being drawn on-demand from another system.

What exactly is the difference between metadata and data in this context?

What data should you store in the catalog and what data should you retain for [webhook](/docs/contributor/webhooks/) callbacks? There is no right or wrong answer to this question. Typically you should aim to store information which has a slow change rate in the catalog and retain other information for a callback.

Here are some examples of how this might operate in practice:

Entity Type | Catalog Data | Live Webhook Data
--- | --- | ---
`thermometer` | `location`, `range`, `unit` | `value`
`bus` | `route`, `seats`, `operator` | `location`
`car-park` | `name`, `location`, `capacity` | `occupancy`

To be clear, data connectors can store _everything_ in the catalog. This has the advantage that everything becomes searchable and they no longer need to host a [webhook](/docs/contributor/webhooks/).

But, on the flip side, they have taken on the burden of sending constant updates to the catalog in order to keep it up to date. Here the catalog is always likely to be a temporally lagging copy of the source stores.

Ultimately the decision on how you use the catalog is up to the coordinators and data connector authors.
