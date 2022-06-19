---
title: "Data Sharing Policy"
linkTitle: "Policies"
weight: 4
description: APIs for creating and manipulating policies
---

Data Sharing Policies are a main component of the BitBroker system. You will find [more details about policies](/docs/concepts/policy/), within the [key concepts](/docs/concepts/) section of this documentation.

{{% alert color="primary" %}}
All API calls in BitBroker require [authorisation](/docs/api-conventions/authorisation/). The sample calls below contain a placeholder string where you should insert your [coordinator API token](/docs/api-conventions/authorisation/#obtaining-a-coordinator-key). If you already have a token, enter it in the box below to update all the sample calls on this page:<br/><br/>_Your Coordinator API Token_<br/><input class="code-replace" data-item="your-token-goes-here" data-name="token" type="text" size="64" placeholder="paste token here">
{{% /alert %}}

## Creating a New Policy

New policies can be created by issuing an `HTTP/POST` to the `/policy/:pid` end-point.

In order to create a policy, you must select a unique policy ID (`pid`) for it.

```shell
curl http://bbk-coordinator:8001/v1/policy/over-a-billion \
     --request POST \
     --include \
     --header "Content-Type: application/json" \
     --header "x-bbk-auth-token: your-token-goes-here" \
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

This will result in a response as follows:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/policy/country
```

The following validation rules will be applied for the body of a new policy request.

Attribute | Necessity | Validation Rules
--- | --- | ---
`pid` | <div class="stamp">required</div> | String between 3 and 32 characters long<br/>Consisting of lowercase letters, numbers and dashes only <br/>Starting with a lowercase letter<br/>Conforming to the regex expression `^[a-z][a-z0-9-]+$`
`name` | <div class="stamp">required</div> | String between 1 and 64 characters long
`description` | <div class="stamp">required</div> | String between 1 and 2048 characters long
`access_control` | <div class="stamp">required</div> | An object describing how data can be accessed
`access_control.enabled` | <div class="stamp">required</div> | A boolean string either 'true' or 'false'
`access_control.quota` | <div class="stamp">optional</div> | An object describing allowable data quotas
`access_control.quota.max_number` | <div class="stamp">optional</div> | An integer greater than 0
`access_control.quota.interval_type` | <div class="stamp">optional</div> | One of an enumeration of either 'day' or 'month'
`access_control.rate` | <div class="stamp">optional</div> | An integer greater than 0
`data_segment` | <div class="stamp">required</div> | An object outlining the shared data subset
`data_segment.segment_query` | <div class="stamp">required</div> | A valid [catalog query](/docs/consumer/catalog/), from the [Consumer API](/docs/consumer/)
`data_segment.field_masks` | <div class="stamp">optional</div> | An array of strings<br/>An empty array
`legal_context` | <div class="stamp">optional</div> | An array of 0 to 100 of object outling the legal basis of data sharing
`legal_context.type` | <div class="stamp">required</div> | One of an enumeration 'attribution', 'contact', 'license', 'note', 'source' or 'terms'
`legal_context.text` | <div class="stamp">required</div> | String between 1 and 256 characters long
`legal_context.link` | <div class="stamp">required</div> | String between 1 and 1024 characters long<br/>Must conform to URI format

Details about the _meaning_ of these attributes can be found within the [key concepts](/docs/concepts/policy/) section of this documentation.

{{% alert color="info" %}}
Policy IDs are required to be unique across an operating BitBroker instance.
{{% /alert %}}

{{% alert color="warning" %}}
You should choose your policy IDs (`pid`) with care, as these cannot be changed once created.
{{% /alert %}}

{{% alert color="primary" %}}
Data sharing policies are complex objects. You should refer to the [the detailed explanation](/docs/concepts/policy/) of these of the [key concepts](/docs/concepts/) area, if you are unclear about any aspects of their construction.
{{% /alert %}}

## Updating a Policy

Existing policies can have their profile updated by issuing an `HTTP/PUT` to the `/policy/:pid` end-point.

In order to update a policy, you must know it's policy ID (`pid`).

```shell
curl http://bbk-coordinator:8001/v1/policy/over-a-billion \
     --request PUT \
     --include \
     --header "Content-Type: application/json" \
     --header "x-bbk-auth-token: your-token-goes-here" \
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

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Great care should be taken when updating policies, since keys may have been issued in the previous context. The ability to change the definition of live policies is a powerful feature, but comes with responsibility.
{{% /alert %}}

{{% alert color="info" %}}
The new policy definition will come into play right away (after a small propagation delay). Keys issued prior to the change, will automatically become subject to the new definition.
{{% /alert %}}

The validation rules for updated policy information, are the same as that for [creating new policies](#creating-a-new-policy).

## List of Existing Policies

You can obtain a list of all the existing policies by issuing an `HTTP/GET` to the `/policy` end-point.

```shell
curl http://bbk-coordinator:8001/v1/policy \
     --header "x-bbk-auth-token: your-token-goes-here"
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

You can obtain the details of an existing policy by issuing an `HTTP/GET` to the `/policy/:pid` end-point.

In order to obtain details of a policy, you must know it's policy ID (`pid`).

```shell
curl http://bbk-coordinator:8001/v1/policy/over-a-billion \
     --header "x-bbk-auth-token: your-token-goes-here"
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

Existing policies can be deleted from the system by issuing an `HTTP/DELETE` to the `/policy/:pid` end-point.

In order to delete a policy, you must know it's policy ID (`pid`).

```shell
curl http://bbk-coordinator:8001/v1/policy/over-a-billion \
     --request DELETE \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Any keys which were issued for this policy will no longer return any results for any of the end-points which form the [Consumer API](/docs/consumer/).
{{% /alert %}}
