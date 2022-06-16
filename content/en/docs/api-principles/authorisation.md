---
title: "Authorisation and Tokens"
linkTitle: "Authorisation"
weight: 3
description: How to send authorised requests to BitBroker APIs
---

All the API sets within BitBroker require authorisation by callers. Whilst the process for authorisation is common across the APIs, the context and reasons for authorisation differ:

* [Coordinator API](/docs/coordinator/) - needed to administer a BitBroker instance
* [Contributor API](/docs/contributor/) - needed to contribute data to a designated entity type
* [Consumer API](/docs/consumer/) - needed to access data via a policy definition

Except in some [development modes](/docs/getting-started/installation/), there is no access to BitBroker services without authorisation and hence without having a corresponding authorisation key.

#### Authorising API calls

When you have obtained the relevant authorisation key ([see below](#obtaining-an-authorisation-key)), you can use it to authorise API calls by using a specified HTTP header attribute. The same authorisation header structure is used for all three BitBroker API sets.

```
x-bbk-auth-token: your-token-will-go-here
```

The header must be exactly as appears here with the same casing and without spaces. If you do not correctly specify the authorisation header, or you use an invalid authorisation key, you will get an `HTTP/1.1 401 Unauthorized` error.

##### Testing your Authorisation Key

If can be useful to make test calls to the API to check that your key is valid and that your authorisation header is formatted correctly.

Each of the three API sets have a test end-point which can be used for this purpose. This is simply the base end-point of each service:

API Service | Test End-point
--- | ---
Coordinator API | `http://bbk-coordinator:8001/v1/`
Contributor API | `http://bbk-contributor:8002/v1/`
Consumer API | `http://bbk-consumer:8003/v1/`

In each case, this returns a simple JSON structure as follows:

```js
{
    "now": "2022-06-06T13:43:19.538Z",
    "name": "bit-broker consumer service",
    "base": "http://bbk-consumer:8003/v1",
    "status": "production"
}
```

You are encouraged to make successful test calls to these end-points before launching into more complex scenarios.

#### Obtaining an Authorisation Key

The method used to obtain an authorisation key differs across the three API sets. However, once you have a key, the [mechanics of authorisation](#authorising-api-calls) is the same. All keys are in the form of a long hexadecimal string such as:

```
4735d360-dc03-499c-91ce-68dfc1554691.5d5c9eab-1d9c-4c88-9478-9f4ab35568a7.423c9101-315a-4929-a64c-9b905837799c
```

These keys should be kept by their owner in a secure location and never shared with unauthorized users.

{{% alert color="warning" %}}
BitBroker does not retain an accesible copy of authorisation keys. If keys are lost, new keys will have to be generated as per the instructions below. The lost keys will then be rescinded.
{{% /alert %}}

##### Obtaining a Coordinator Key

Coordinator keys are required to authorise calls to the [Coordinator API](/docs/coordinator/). This API is used to perform administrative services for a BitBroker instance.

Coordinator keys are obtained by utilising end-points on the Coordinator API, in order to promote a user to coordinator status. To do this you must first [create a new user](/docs/coordinator/user/#creating-a-new-user) and then you must [promote that user](/docs/coordinator/user/#promoting-a-user-to-coordinator) to be a coordinator.

Once you promote a user to be a coordinator, then their coordinator key will be returned in the body of the [response to that API call](/docs/coordinator/user/#promoting-a-user-to-coordinator).

{{% alert color="info" %}}
Fresh BitBroker [installations](/docs/getting-started/installation/) come with a bootstrap coordinator user and with an associated [bootstrap coordinator token](/docs/getting-started/installation/#bootstrap-coordinator-token).
{{% /alert %}}

{{% alert color="warning" %}}
It is possible, but _not recommended_, to use the boostrap user in normal operation. Instead, you should use the [bootstrap coordinator token](/docs/getting-started/installation/#bootstrap-coordinator-token) to create your own master coordinator user and then utilisie their key for further operations.
{{% /alert %}}

If a coordinator key is lost, then a new key will have to be generated. This can be done by first [demoting the user](/docs/coordinator/user/#demoting-a-user-from-coordinator) from being a coordinator and then [promoting them](/docs/coordinator/user/#promoting-a-user-to-coordinator) again. Note that, in this scenario, the old coordinator key will be rescinded.

##### Obtaining a Contributor Key

Contributor keys are required to authorise calls to the [Contributor API](/docs/contributor/). This API is used to contribute data to a designated [entity type](/docs/concepts/entity-types/).

Contributor keys are obtained by utilising end-points on the [Coordinator API](/docs/coordinator/), in order to [create a connector](/docs/coordinator/connectors/#creating-a-new-connector) on a given entity type.

Once the connector is created, then its contribution key will be returned in the body of the [response to that API call](/docs/coordinator/connectors/#creating-a-new-connector). More information about [data connectors](/docs/concepts/connectors/) and data contribution is available in the [key concepts](/docs/concepts/) section of this documentation.

If a contributor key is lost, then a new key will have to be generated. This can be done by first [deleting the connector](/docs/coordinator/connectors/#deleting-a-connector) and then [creating it afresh](/docs/coordinator/connectors/#creating-a-new-connector). Note that, in this scenario, the old contributor key will be rescinded.

{{% alert color="warning" %}}
Losing contributor keys can cause major distruption. Since the resolution involves deletion and the re-creation of the connector, all the associated data records will be lost and will have to be reinserted into [the catalog](/docs/concepts/catalog/) by the new connector.
{{% /alert %}}

{{% alert color="info" %}}
Recreating a connector which was previously deleted, will result in the same [connector ID](todo), if the new connector has the exact same name.
{{% /alert %}}

It is expected that the coordinator user who creates the data connector, will distribute the contribution key in a secure manner to the relevant party.

##### Obtaining a Consumer Key

Consumer keys are required to authorise calls to the [Consumer API](/docs/consumer/). This API is used to access data via a policy definition.

Consumer keys are obtained by utilising end-points on the [Coordinator API](/docs/coordinator/). To do this you must [create an access](/docs/coordinator/access/#creating-a-new-access), which is a link between a user and a policy defintion.

Once you create such an access, then the consumer key will be returned in the body of the [response to that API call](/docs/coordinator/access/#creating-a-new-access).

If a consumer key is lost, then you can [reissue the access](/docs/coordinator/access/#reissuing-an-access) to obtain a new key. Note that, in this scenario, the old consumer key will be rescinded.
