---
title: "Authorisation and Tokens"
linkTitle: "Authorisation"
weight: 3
description: How to send authorised requests to BitBroker APIs
---

All the API sets within BitBroker require authorisation by callers. Whilst the process of authorisation is common across them all, the context and reasons for authorisation differ.

* [Coordinator API](todo) - needed to administer the BitBroker instance
* [Contributor API](todo) - needed to contribute data to a designated entity type
* [Consumer API](todo) - needed to access data via a policy definition

Except in some [developement modes](todo), there is no access to BitBroker services without authorisation and hence without having a corresponding access key.

#### Authorising API calls

When you have obtained the relevant access key ([see below](todo)), you can use it to authorise API calls by using a specified HTTP header attribute. The same authorisation header structure is used for all three BitBroker API sets.

```
x-bbk-auth-token: your-token-will-go-here
```

The header must be exactly as appears here with the same casing and without spaces. If you do not correctly specify the authorisation header, or you use an invalid access key, you will get an `HTTP/1.1 401 Unauthorized` error.

##### Testing your Access Key

If can be useful to make test calls to the API to check that your authorisation header is formatted correctly. Each of the three API sets have a test end-point which can be used for this purpose. This is simple the base end-point of each service:

API Service | Test End-point
--- | ---
coordinator | `http://bbk-coordinator:8001/v1/`
contributor | `http://bbk-contributor:8002/v1/`
consumer | `http://bbk-consumer:8003/v1/`

In each case, this returns a simple JSON structure as follows:

```js
{
    "now": "2022-06-06T13:43:19.538Z",
    "name": "bit-broker consumer service",
    "base": "http://bbk-consumer:8003/v1",
    "status": "development"
}
```

You are encouraged to first make successful test calls to these end-points before launching into more complex scenarios.

#### Obtaining an Access Key

The method used to obtain an access key differs across the three API sets. However, once you have a key, [mechanics of authorisation](todo) is the same. All access keys are in the form of a long hexadecimal string such as:

```
4735d360-dc03-499c-91ce-68dfc1554691.5d5c9eab-1d9c-4c88-9478-9f4ab35568a7.423c9101-315a-4929-a64c-9b905837799c
```

These keys should be kept by the owner in a secure location and never shared with unauthorized people.

{{% alert color="warning" %}}
BitBroker does not retain an accesible copy of authorisation keys. If keys are lost then, they new keys will have to be generated as per the instructions below.
{{% /alert %}}

##### Obtaining a Coordinator Access key

Coordinator access keys are required to authorise calls to the [Coordinator API](todo). This API is used to perform key administration services for a BitBroker instance.

Coordinator keys are obtained by utilising end-points on the Coordinator API, in order to promote a user to coordinator status. To do this you must first [create a new user](todo) and then you must [promote that user](todo) to be a coordinator.

Once you promote a user to be a coordinator, then their coordinator access key will be returned in the body of the [response to that API call](todo).

{{% alert color="info" %}}
Fresh BitBroker installations come with a [bootstrap coordinator](todo) and with an associated [coordinator acess key](todo). It is possible, but _not recommended_ to use this boostrap user in normal operation. Instead, you should use this key to create your own master coordinator user and then utilisie their key for further operations.
{{% /alert %}}

If a coordinator key is lost, then a new key will have to be generated. This can be done by first [demoting the user](todo) from being a coordinator and then [promoting them](todo) again. Note that, in this scenario, the old coordinator key will be rescinded.

##### Obtaining a Contributor Access key

Contributor access keys are required to authorise calls to the [Contributor API](todo). This API is used to contribute data to a designated entity type.

Contributor keys are obtained by utilising end-points on the Coordinator API, in order to [create a connector](todo) on a given entity type.

Once the connector is created, then its contribtion access key will be returned in the body of the [response to that API call](todo). More information about [data connectors](todo) and [data contribution](todo) is available on in the [ke concepts](todo) section of this documentation.

If a contributor key is lost, then a new key will have to be generated. This can be done by first [deleting the connector](todo) and then [creating it afresh](todo). Note that, in this scenario, the old contributor key will be rescinded.

{{% alert color="warning" %}}
Losing contributor keys will cause major distruption. Since the steps involved deletion and the re-creation of the connector, all the associated data records will be lost and will have to be reinserted into [the catalog](todo).
{{% /alert %}}

{{% alert color="info" %}}
Recreating a connector which was previously deleted will result in the same [connector ID](todo), if the new connector has the exact same name.
{{% /alert %}}

It is expected that the coordinator user who creates the data connector, will also distribute the contribution key in a secure manner to the relevant party.

##### Obtaining a Consumer Access key

Consumer access keys are required to authorise calls to the [Consumer API](todo). This API is used to access data via a policy definition.

Consumer keys are obtained by utilising end-points on the Coordinator API. To do this you must [create an access](todo) between a user and a policy defintion.

Once you create such an access, then the consumer access key will be returned in the body of the [response to that API call](todo).

If a consumer key is lost, then you can [reissue the access](todo) to obtain a new key. Note that, in this scenario, the old consumer key will be rescinded.
