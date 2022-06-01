---
title: "Data Sharing Policy"
linkTitle: "Policies"
weight: 4
description: APIs for creating and manipulating policies
---

Data Sharing Policies are a main component of the BitBroker system. You will find [more details about policies](todo), within the [key concepts](todo) section of this documentation.

Before you use this API, you should become familiar with the general, system-wide [API principles](todo) - which are used across all three BitBroker API sets. These principles include topics such as [standard logical server names and ports](todo), [RESTful designs](todo), [authorisation](todo), [error handling](todo), etc.

## Creating a New Policy

New policies can be created by issuing an `HTTP/POST` to the `/policy/:pid` end-point. In order to create a policy, you must select a unique policy ID (pid) for it (see later section on validation rules).

```shell
curl http://bbk-coordinator:8001/v1/policy/over-a-billion \
     --request POST \
     --include \
     --header "Content-Type: application/json" \
     --data-binary @- << EOF
     { \
         "name": "The Most Populated Countries", \
         "description": "Countries with a population of over a billion", \
         "policy": { \
             "access_control": { \
                 "enabled": true, \
                 "quota": { \
                     "max_number": 86400, \
                     "interval_type": "day" \
                 }, \
                 "rate": 250 \
             }, \
             "data_segment": { \
                 "segment_query": { \
                     "type": "country", \
                     "entity.population": { "$gt": 1000000000 } \
                 }, \
                 "field_masks": [] \
             }, \
             "legal_context": [ { \
                     "type": "attribution", \
                     "text": "Data is supplied by Wikipedia", \
                     "link": "https://en.wikipedia.org/" \
                 } \
             ] \
         } \
     }
EOF
```

This will result in:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/policy/country
```

The following validation rules exist for the body of a new policy request.

Attribute | Necessity | Validation Rules
--- | --- | ---
`pid` | <div class="stamp">required</div> | String between 3 and 32 characters long<br/>Consisting of lowercase letters, numbers and dashes only <br/>Starting with a lowercase letter<br/>Conforming to the regex expression `^[a-z][a-z0-9-]+$`
`name` | <div class="stamp">required</div> | String between 1 and 64 characters long
`description` | <div class="stamp">required</div> | String between 1 and 8192 characters long
`access_control` | <div class="stamp">required</div> | An object describing how data can be accessed
`access_control.enabled` | <div class="stamp">required</div> | A boolean string either 'true' or 'false'
`access_control.quota` | <div class="stamp">optional</div> | An object describing how data quotas
`access_control.quota.max_number` | <div class="stamp">optional</div> | An integer greater than 0
`access_control.quota.interval_type` | <div class="stamp">optional</div> | One of an enumeration of either 'day' or 'month'
`access_control.rate` | <div class="stamp">optional</div> | An integer greater than 0
`data_segment` | <div class="stamp">required</div> | An object outlining the shared data subset
`data_segment.segment_query` | <div class="stamp">required</div> | A valid [catalog query](todo), from the [Consumer API](todo)
`data_segment.field_masks` | <div class="stamp">optional</div> | An array of strings<br/>An empty array
`legal_context` | <div class="stamp">optional</div> | An array of 0 to 100 of object outling the legal basis of data sharing
`legal_context.type` | <div class="stamp">required</div> | One of an enumeration 'attribution', 'contact', 'license', 'note', 'source' or 'terms'
`legal_context.text` | <div class="stamp">required</div> | String between 1 and 8192 characters long
`legal_context.link` | <div class="stamp">required</div> | String between 1 and 1024 characters long<br/>Must conform to URI format

{{% alert color="info" %}}
Policy IDs need to be unique across an operating BitBroker instance.
{{% /alert %}}

{{% alert color="warning" %}}
You should choose your policy IDs with care as these cannot be changed once created.
{{% /alert %}}

{{% alert color="primary" %}}
Data sharing policies are complex objects. You should refer to the [the detailed explanation](todo) of these om the [key concepts](todo) area, if you are unclear about any aspects of their construction.
{{% /alert %}}

## Updating a Policy

Existing policies can have their profile updated by issuing an `HTTP/PUT` to the `/policy/:pid` end-point. In order to update a policy, you must know it's policy ID (pid).

```shell
curl http://bbk-coordinator:8001/v1/policy/over-a-billion \
     --request PUT \
     --include \
     --header "Content-Type: application/json" \
     --data-binary @- << EOF
     { \
         "name": "This is a new name", \
         "description": "This is a new description", \
         "policy": { \
             "access_control": { \
                 "enabled": true, \
                 "quota": { \
                     "max_number": 86400, \
                     "interval_type": "day" \
                 }, \
                 "rate": 250 \
             }, \
             "data_segment": { \
                 "segment_query": { \
                     "type": "country", \
                     "entity.population": { "$gt": 1000000000 } \
                 }, \
                 "field_masks": [] \
             }, \
             "legal_context": [ { \
                     "type": "attribution", \
                     "text": "Data is supplied by Wikipedia", \
                     "link": "https://en.wikipedia.org/" \
                 } \
             ] \
         } \
     }
EOF
```

This will result in:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Great care should be taken when updating policies, since keys may have been issued in the previous context. The ability to change the definition to live policies is a key feature, but comes with responsibility. The new policy definition will come into play right away, after a small propagation delay. Keys issued prior to the change, will become subject to definition.
{{% /alert %}}

The validation rules for updated policy information, is the same as that for [creating new policies](#creating-a-new-policy).

## List of Existing Policies

You can obtain a list of all the existing policies by issuing an `HTTP/GET` to the `/policy` end-point.

```shell
curl http://bbk-coordinator:8001/v1/policy
```

This will return a JSON array as follows:

```js
[
    {
        "id": "over-a-billion",
        "url": "http://bbk-coordinator:8001/v1/policy/over-a-billion",
        "name": "This is a new name",
        "description": "This is a new description"
    }
    // ... other policies here
]
```

Each policy on the system will be returned within this array. Note: There is currently no paging on this API.

## Details of an Existing Policy

You can obtain the details of an existing policy by issuing an `HTTP/GET` to the `/policy/:pid` end-point. In order to obtain details of a policy, you must know it's policy ID (pid).

```shell
curl http://bbk-coordinator:8001/v1/policy/over-a-billion
```

This will return a JSON object as follows:

```js
{
    "id": "over-a-billion",
    "url": "http://bbk-coordinator:8001/v1/policy/over-a-billion",
    "name": "This is a new name",
    "description": "This is a new description",
    "policy": {
        "data_segment": {
            "field_masks": [],
            "segment_query": {
                "type": "country",
                "entity.population": {
                    "$gt": 1000000000
                }
            }
        },
        "legal_context": [
            {
                "link": "https://en.wikipedia.org/",
                "text": "Data is supplied by Wikipedia",
                "type": "attribution"
            }
        ],
        "access_control": {
            "rate": 250,
            "quota": {
                "max_number": 86400,
                "interval_type": "day"
            },
            "enabled": true
        }
    }
}
```

## Deleting a Policy

Existing policies can be deleted from the system by issuing an `HTTP/DELETE` to the `/policy/:pid` end-point. In order to delete a policy, you must know it's policy ID (pid).

```shell
curl --request DELETE \
     --include \
     http://bbk-coordinator:8001/v1/policy/over-a-billion
```

This will result in:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Any keys which were issued for this policy will no longer return any results for any of the end-points which form the [Consumer API](todo).
{{% /alert %}}
