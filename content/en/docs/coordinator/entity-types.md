---
title: "Entity Types"
linkTitle: "Entity Types"
weight: 2
description: APIs for creating and manipulating entity types
---

Entity Types are a main component of the BitBroker system. You will find [more details about entity types](/docs/concepts/entity-types/), within the [key concepts](/docs/concepts/) section of this documentation.

{{% alert color="primary" %}}
All API calls in BitBroker require [authorisation](/docs/api-principles/authorisation/). The sample calls below contain a placeholder string where you should insert your [coordinator API token](/docs/api-principles/authorisation/#obtaining-a-coordinator-key). If you already have a token, enter it in the box below to update all the sample calls on this page:<br/><br/>_Your Coordinator API Token_<br/><input class="code-replace" data-item="your-token-goes-here" data-name="token" type="text" size="64" placeholder="paste token here">
{{% /alert %}}

## Creating a New Entity Type

New entity types can be created by issuing an `HTTP/POST` to the `/entity/:eid` end-point.

In order to create an entity type, you must select a unique entity type ID (`eid`) for it.

```shell
curl http://bbk-coordinator:8001/v1/entity/country \
     --request POST \
     --include \
     --header "Content-Type: application/json" \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-binary @- << EOF
     { \
         "name": "Countries",  \
         "description": "All the countries in the world as defined by the UN", \
         "schema": {}, \
         "timeseries": { \
             "population": { \
                 "period": "P1Y", \
                 "value": "people", \
                 "unit": "x1000" \
             } \
         } \
     }
EOF
```

This will result in a response as follows:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/entity/country
```

The following validation rules will be applied for the body of a new entity type request.

Attribute | Necessity | Validation Rules
--- | --- | ---
`eid` | <div class="stamp">required</div> | String between 3 and 32 characters long<br/>Consisting of lowercase letters, numbers and dashes only <br/>Starting with a lowercase letter<br/>Conforming to the regex expression `^[a-z][a-z0-9-]+$`
`name` | <div class="stamp">required</div> | String between 1 and 64 characters long
`description` | <div class="stamp">required</div> | String between 1 and 2048 characters long
`schema` | <div class="stamp">optional</div> | A valid [JSON Schema](https://json-schema.org/) document
`timeseries` | <div class="stamp">optional</div> | A list of timeseries which are present on this entity type
`timeseries.id` | <div class="stamp">required</div> | String between 3 and 32 characters long<br/>Consisting of lowercase letters, numbers and dashes only <br/>Starting with a lowercase letter<br/>Conforming to the regex expression `^[a-z][a-z0-9-]+$`
`timeseries.period` | <div class="stamp">required</div> | A string conforming to the [ISO8601 duration](https://en.wikipedia.org/wiki/ISO_8601#Durations) format
`timeseries.value` | <div class="stamp">required</div> | String between 1 and 256 characters long
`timeseries.unit` | <div class="stamp">required</div> | String between 1 and 256 characters long

Details about the _meaning_ of these attributes can be found within the [key concepts](/docs/concepts/entity-types/) section of this documentation. The `schema` attribute is a powerful concept, which is [explained in more detail](/docs/concepts/entity-types/#entity-schemas) there.

{{% alert color="info" %}}
Entity type IDs are required to be unique across an operating BitBroker instance.
{{% /alert %}}

{{% alert color="info" %}}
Timeseries IDs are required to be unique within their housing entity type.
{{% /alert %}}

{{% alert color="info" %}}
By convention, entity types are expressed in the singular, non-plural form. So, for example, `product` is preferred over `products`.
{{% /alert %}}

{{% alert color="warning" %}}
You should choose your entity type IDs (`eid`) with care, as these cannot be changed once created.
{{% /alert %}}

## Updating an Entity Type

Existing entity types can have their profile updated by issuing an `HTTP/PUT` to the `/entity/:eid` end-point.

In order to update an entity type, you must know it's entity type ID (`eid`).

```shell
curl http://bbk-coordinator:8001/v1/entity/country \
     --request PUT \
     --include \
     --header "Content-Type: application/json" \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-binary @- << EOF
     { \
         "name": "Countries",  \
         "description": "This is a new description for countries", \
         "schema": {}, \
         "timeseries": { \
             "population": { \
                 "period": "P1Y", \
                 "value": "people", \
                 "unit": "x1000" \
             } \
         } \
     }
EOF
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Great care should be taken when updating the JSON `schema` associated with an entity type. Consider whether this breaks an implicit contract that you may have made with existing [data connectors](/docs/concepts/connectors/) for this entity type. It may make more sense to create a new entity type, if the amended `schema` definition is incompatible with the existing one.
{{% /alert %}}

The validation rules for updated entity type information, are the same as that for [creating new entity types](#creating-a-new-entity-type).

## List of Existing Entity Types

You can obtain a list of all the existing entity types by issuing an `HTTP/GET` to the `/entity` end-point.

```shell
curl http://bbk-coordinator:8001/v1/entity \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return a JSON array as follows:

```js
[
    {
        "id": "country",
        "url": "http://bbk-coordinator:8001/v1/entity/country",
        "name": "Countries",
        "description": "This is a new description for countries"
    }
    // ... other entity types here
]
```

Each entity type on the system will be returned within this array. Note: There is currently no paging on this API.

## Details of an Existing Entity Type

You can obtain the details of an existing entity type by issuing an `HTTP/GET` to the `/entity/:eid` end-point.

In order to obtain details of an entity type, you must know it's entity type ID (`eid`).

```shell
curl http://bbk-coordinator:8001/v1/entity/country \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return a JSON object as follows:

```js
{
    "id": "country",
    "url": "http://bbk-coordinator:8001/v1/entity/country",
    "name": "Countries",
    "description": "This is a new description for countries",
    "schema": {},
    "timeseries": {
        "population": {
            "unit": "x1000",
            "value": "people",
            "period": "P1Y"
        }
    }
}
```

## Deleting an Entity Type

Existing entity types can be deleted from the system by issuing an `HTTP/DELETE` to the `/entity/:eid` end-point.

In order to delete an entity type, you must know it's entity type ID (`eid`).

```shell
curl http://bbk-coordinator:8001/v1/entity/country \
     --request DELETE \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Deleting an entity type will also delete all entity instances associated with that type from [the Catalog](/docs/concepts/catalog/).
{{% /alert %}}

{{% alert color="warning" %}}
Deleting an entity type will also delete all [data connectors](/docs/concepts/connectors/) associated with that entity type. No further [data contribution](/docs/contributor/) will be accepted for it.
{{% /alert %}}

{{% alert color="info" %}}
Any [policy](/docs/concepts/policy/) keys which were issued where this entity type formed part of the [data segment](/docs/concepts/policy/#data-segment), will no longer return entity instances of this type. In some circumstances, this could render policy keys unfit for purpose.
{{% /alert %}}
