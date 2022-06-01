---
title: "Connectors"
linkTitle: "Connectors"
weight: 3
description: APIs for creating and manipulating data connectors
---

Connectors are a main component of the BitBroker system. You will find [more details about connectors](todo), within the [key concepts](todo) section of this documentation.

Connectors are always created within the context of housing [entity types](todo), which can be created and manipulated using [other APIs](todo) described earlier in this documentation.

{{% alert color="info" %}}
In order to use the sample calls in this section, first [create the housing entity](todo) as outlined in the previous section.
{{% /alert %}}

Before you use this API, you should become familiar with the general, system-wide [API principles](todo) - which are used across all three BitBroker API sets. These principles include topics such as [standard logical server names and ports](todo), [RESTful designs](todo), [authorisation](todo), [error handling](todo), etc.

## Creating a New Connector

New connectors can be created by issuing an `HTTP/POST` to the `/entity/:eid/connector/:cid` end-point. Connectors are always created within the context of a housing entity type and, hence, you must know its ID (eid). In order to create a connector, you must select a unique connector ID (cid) for it (see later section on validation rules).

```shell
curl http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia \
     --request POST \
     --include \
     --header "Content-Type: application/json" \
     --data-binary @- << EOF
     { \
         "name": "Wikipedia", \
         "description": "Country information from Wikipedia", \
         "webhook": "http://my-domain.com/connectors/1", \
         "cache": 0 \
     }
EOF
```

This will result in:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia
```

The body of this response will contain the [connector ID](todo) and [connector key](todo) which the new data connector should utilise to make it's [data contributions](todo). For example:

```js
{
    "id":"9afcf3235500836c6fcd9e82110dbc05ffbb734b",
    "token":"ef6ba361-ef55-4a7a-ae48-b4ecef9dabb5.5ab6b7a2-6a50-4d0a-a6b4-f43dd6fe12d9.7777df3f-e26b-4f4e-8c80-628f915871b4"
}
```

It is expected that coordinator user will distribute this connector ID and key to the operator of the new data connector.

{{% alert color="warning" %}}
Connector keys are not stored within the system. If you lose this connector key, you will be forced to delete and recreate the connector to obtain a new one.
{{% /alert %}}

The following validation rules exist for the body of a new connector request.

Attribute | Necessity | Validation Rules
--- | --- | ---
`cid` | <div class="stamp">required</div> | String between 3 and 32 characters long<br/>Consisting of lowercase letters, numbers and dashes only <br/>Starting with a lowercase letter<br/>Conforming to the regex expression `^[a-z][a-z0-9-]+$`
`name` | <div class="stamp">required</div> | String between 1 and 64 characters long
`description` | <div class="stamp">required</div> | String between 1 and 8192 characters long
`webhook` | <div class="stamp">optional</div> | String between 1 and 256 characters long<br/>Must conform to URI format
`cache` | <div class="stamp">optional</div> | Integer between 0 and 31536000

{{% alert color="info" %}}
Connector IDs need to be unique across an operating BitBroker instance.
{{% /alert %}}

You should choose your connector IDs with care as these cannot be changed once created.

## Updating a connector

Existing connectors can have their profile updated by issuing an `HTTP/PUT` to the `/entity/:eid/connector/:cid` end-point. In order to update a connector, you must know it's connector ID (cid).

```shell
curl http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia \
     --request PUT \
     --include \
     --header "Content-Type: application/json" \
     --data-binary @- << EOF
     { \
         "name": "Wikipedia", \
         "description": "A new description for the Wikipedia connector", \
         "webhook": "http://my-domain.com/connectors/1", \
         "cache": 0 \
     }
EOF
```

This will result in:

```
HTTP/1.1 204 No Content
```

The validation rules for updated connector information, is the same as that for [creating new connectors](#creating-a-new-connector).

## List of Existing Connectors

You can obtain a list of all the existing connectors housed within a parent entity type by issuing an `HTTP/GET` to the `/entity/:eid/connector` end-point.

```shell
curl http://bbk-coordinator:8001/v1/entity/country/connector
```

This will return a JSON array as follows:

```js
[
    {
        "id": "wikipedia",
        "url": "http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia",
        "name": "Wikipedia",
        "description": "A new description for the Wikipedia connector"
    }
    // ... other connectors here
]
```

Each connector is housed within a parent entity type will be returned within this array. Note: There is currently no paging on this API.

## Details of an Existing Connector

You can obtain the details of an existing connector by issuing an `HTTP/GET` to the `/entity/:eid/connector/:cid` end-point. In order to obtain details of a connector, you must know it's connector ID (cid).

```shell
curl http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia
```

This will return a JSON object as follows:

```js
{
    "id": "wikipedia",
    "url": "http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia",
    "name": "Wikipedia",
    "description": "A new description for the Wikipedia connector",
    "entity": {
        "id": "country",
        "url": "http://bbk-coordinator:8001/v1/entity/country"
    },
    "contribution_id": "9afcf3235500836c6fcd9e82110dbc05ffbb734b",
    "webhook": "http://my-domain.com/connectors/1",
    "cache": 0,
    "is_live": false,
    "in_session": false
}
```

{{% alert color="info" %}}
Connector keys are not stored within the system and hence not present in this returned document.
{{% /alert %}}

Later sections of this document will explain what the [is_live](todo) and [in_session](todo) attributes refer to.

## Promoting a Connector to Live

Connectors can be promoted to live status by issuing an `HTTP/POST` to the `/entity/:eid/connector/:cid/live` end-point. In order to promote a connector, you must know it's connector ID (cid).

```shell
curl http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia/live \
     --request POST \
     --include
```

This will result in:

```
HTTP/1.1 204 No Content
```

When getting details for promoted connectors, their is_live status will be reflected in the corresponding attribute:

```js
{
    "id": "wikipedia",
    "url": "http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia",
    "name": "Wikipedia",
    "description": "A new description for the Wikipedia connector",
    "entity": {
        "id": "country",
        "url": "http://bbk-coordinator:8001/v1/entity/country"
    },
    "contribution_id": "9afcf3235500836c6fcd9e82110dbc05ffbb734b",
    "webhook": "http://my-domain.com/connectors/1",
    "cache": 0,
    "is_live": true,
    "in_session": false
}
```

{{% alert color="info" %}}
If you attempt to promote a connector which is already live, this will still result in an `HTTP/1.1 204 No Content`. Such requests are benign and will not impact the connector status.
{{% /alert %}}

{{% alert color="warning" %}}
Promoting connectors will cause all the staged records which they have contributed to [the Catalog](todo) to become visible for calls to the [Consumer API](todo).
{{% /alert %}}

## Demoting a Connector from Live

Existing connectors can be demoted from live status by issuing an `HTTP/DELETE` to the `/entity/:eid/connector/:cid/live` end-point. In order to demote a connector, you must know it's connector ID (cid).

```shell
curl http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia/live \
     --request DELETE \
     --include
```

This will result in:

```
HTTP/1.1 204 No Content
```

When getting details for such users, their live status will be reflected in the corresponding attribute:

```js
{
    "id": "wikipedia",
    "url": "http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia",
    "name": "Wikipedia",
    "description": "A new description for the Wikipedia connector",
    "entity": {
        "id": "country",
        "url": "http://bbk-coordinator:8001/v1/entity/country"
    },
    "contribution_id": "9afcf3235500836c6fcd9e82110dbc05ffbb734b",
    "webhook": "http://my-domain.com/connectors/1",
    "cache": 0,
    "is_live": false,
    "in_session": false
}
```

{{% alert color="info" %}}
If you attempt to demote a connector which is not live, this will still result in an `HTTP/1.1 204 No Content`. Such requests are benign and will not impact the connector status.
{{% /alert %}}

{{% alert color="warning" %}}
Demoting connectors will remove all the records which they have contributed to [the Catalog](todo) from calls to the [Consumer API](todo). Users holding on to full qualified URLs to such records will instead receive `HTTP/1.1 404 Not Found` responses.
{{% /alert %}}

## Deleting a connector

Existing connectors can be deleted from the system by issuing an `HTTP/DELETE` to the `/entity/:eid/connector/:cid` end-point. In order to delete a connector, you must know it's connector ID (cid).

```shell
curl --request DELETE \
     --include \
     http://bbk-coordinator:8001/v1/entity/country/connector/wikipedia
```

This will result in:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Deleting a connector will also remove all the records which it has contributed to the [the Catalog](todo).
{{% /alert %}}

{{% alert color="warning" %}}
Deleting a connector will also remove its ability to contribute data. No further contribution will be accepted and all subsequent calls to the [Contributor API](todo) will fail.
{{% /alert %}}

{{% alert color="info" %}}
Any policy keys which were issued where this connector's records formed part of the data segment, will no longer return entity instances contributed by it. In some circumstances, this could render policy keys unfit for purpose.
{{% /alert %}}
