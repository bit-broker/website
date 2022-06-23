---
title: "Authorization and Tokens"
linkTitle: "Authorization"
weight: 2
description: How to send authorized requests to BitBroker APIs
---

All the API sets within BitBroker require authorization by callers. Whilst the process for authorization is common across the APIs, the context and reasons for authorization differ:

* [Coordinator API](/docs/coordinator/) - needed to administer a BitBroker instance
* [Contributor API](/docs/contributor/) - needed to contribute data to a designated entity type
* [Consumer API](/docs/consumer/) - needed to access data via a policy definition

Except in some [development modes](/docs/getting-started/install-local/#development-only-headers), there is no access to BitBroker services without authorization and hence without having a corresponding authorization token.

#### Authorizing API calls

When you have obtained the relevant authorization token ([see below](#obtaining-an-authorization-token)), you can use it to authorize API calls by using a specified HTTP header attribute. The same authorization header structure is used for all three BitBroker API sets.

```
x-bbk-auth-token: your-token-will-go-here
```

The header must be exactly as it appears here, with the same casing and without spaces. If you do not correctly specify the authorization header, or you use an invalid authorization token, you will get an `HTTP/1.1 401 Unauthorized` error.

##### Testing your Authorization Token

It can be useful to make test calls to the API to check that your key is valid and that your authorization header is formatted correctly.

The base end-point of all the three API servers respond with a small announcement. Like all BitBroker API end-points, these require a working authorization to be in place. Hence, this announcement can be used for testing or verification purposes.

See the section on testing installations for [Kubernetes](/docs/getting-started/install-k8s/#testing-your-installation) or [local](/docs/getting-started/install-local/#testing-your-installation) modes for more details.

You are encouraged to make successful test calls to these end-points before launching into more complex scenarios.

#### Obtaining an Authorization Token

The method used to obtain an authorization token differs across the three API sets. However, once you have a key, the [mechanics of authorization](#authorising-api-calls) are the same. All keys are in the form of a long hexadecimal string, such as:

```
4735d360-dc03-499c-91ce-68dfc1554691.5d5c9eab-1d9c-4c88-9478-9f4ab35568a7.423c9101-315a-4929-a64c-9b905837799c
```

These keys should be kept by their owner in a secure location and never shared with unauthorized users.

{{% alert color="warning" %}}
BitBroker does not retain an accessible copy of authorization tokens. If keys are lost, new keys will have to be generated as per the instructions below. The lost keys will then be rescinded.
{{% /alert %}}

##### Obtaining a Coordinator Authorization Token

Coordinator authorization tokens are required to authorize calls to the [Coordinator API](/docs/coordinator/). This API is used to perform administrative services for a BitBroker instance.

Coordinator authorization tokens are obtained by utilizing end-points on the Coordinator API, in order to promote a user to coordinator status. To do this you must first [create a new user](/docs/coordinator/user/#creating-a-new-user) and then you must [promote that user](/docs/coordinator/user/#promoting-a-user-to-coordinator) to be a coordinator.

Once you promote a user to be a coordinator, then their coordinator authorization token will be returned in the body of the [response to that API call](/docs/coordinator/user/#promoting-a-user-to-coordinator).

{{% alert color="info" %}}
Fresh BitBroker [installations](/docs/getting-started/installation/) come with a bootstrap coordinator user and with an associated [bootstrap coordinator token](/docs/getting-started/installation/#bootstrap-coordinator-token).
{{% /alert %}}

{{% alert color="warning" %}}
It is possible, but _not recommended_, to use the bootstrap user in normal operation. Instead, you should use the [bootstrap coordinator token](/docs/getting-started/installation/#bootstrap-coordinator-token) to create your own master coordinator user and then utilize their token for further operations.
{{% /alert %}}

If a coordinator authorization token is lost, then a new token will have to be generated. This can be done by first [demoting the user](/docs/coordinator/user/#demoting-a-user-from-coordinator) from being a coordinator and then [promoting them](/docs/coordinator/user/#promoting-a-user-to-coordinator) again. Note that, in this scenario, the old coordinator authorization token will be rescinded.

##### Obtaining a Contributor Key

Contributor keys are required to authorize calls to the [Contributor API](/docs/contributor/). This API is used to contribute data to a designated [entity type](/docs/concepts/entity-types/).

Contributor keys are obtained by utilizing end-points on the [Coordinator API](/docs/coordinator/), in order to [create a connector](/docs/coordinator/connectors/#creating-a-new-connector) on a given entity type.

Once the connector is created, then its contribution key will be returned in the body of the [response to that API call](/docs/coordinator/connectors/#creating-a-new-connector). More information about [data connectors](/docs/concepts/connectors/) and data contribution is available in the [key concepts](/docs/concepts/) section of this documentation.

If a contributor key is lost, then a new key will have to be generated. This can be done by first [deleting the connector](/docs/coordinator/connectors/#deleting-a-connector) and then [creating it afresh](/docs/coordinator/connectors/#creating-a-new-connector). Note that, in this scenario, the old contributor key will be rescinded.

{{% alert color="warning" %}}
Losing contributor keys can cause major disruption. Since the resolution involves deletion and the re-creation of the connector, all the associated data records will be lost and will have to be reinserted into [the catalog](/docs/concepts/catalog/) by the new connector.
{{% /alert %}}

{{% alert color="info" %}}
Recreating a connector which was previously deleted, will result in the same connector ID, if the new connector has the exact same name.
{{% /alert %}}

It is expected that the coordinator user, who creates the data connector, will distribute the contribution key in a secure manner to the relevant party.

##### Obtaining a Consumer Token

Consumer authorization tokens are required to authorize calls to the [Consumer API](/docs/consumer/). This API is used to access data via a policy definition.

Consumer authorization tokens are obtained by utilizing end-points on the [Coordinator API](/docs/coordinator/). To do this you must [create an access](/docs/coordinator/access/#creating-a-new-access), which is a link between a user and a policy definition.

Once you create such an access, then the consumer authorization token will be returned in the body of the [response to that API call](/docs/coordinator/access/#creating-a-new-access).

If a consumer authorization token is lost, then you can [reissue the access](/docs/coordinator/access/#reissuing-an-access) to obtain a new token. Note that, in this scenario, the old consumer authorization token will be rescinded.
