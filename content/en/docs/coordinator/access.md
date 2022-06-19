---
title: "User Data Access"
linkTitle: "Access"
weight: 5
description: APIs for creating and managing user's data access
---

Data access is a main component of the BitBroker system. You will find [more details about data access](/docs/concepts/access/), within the [key concepts](/docs/concepts/) section of this documentation.

Accesses are always created within the context of a [user](/docs/concepts/users/) and a [policy](/docs/concepts/policy/). These can be created and manipulated using other parts of this API, described elsewhere in this documentation.

{{% alert color="primary" %}}
In order to use the sample calls in this section, you must create first the [user](/docs/coordinator/user/#creating-a-new-user) and the [policy](/docs/coordinator/policy/#creating-a-new-policy), as outlined in the previous sections.
{{% /alert %}}

{{% alert color="primary" %}}
In our sample calls, we use the standard [server name and port](/docs/getting-started/install-local/#server-naming-and-ports) format outlined for [local installations](/docs/getting-started/install-local/) and [demos](/docs/getting-started/demo/). If you have an alternative API host base URL, then enter it in the box below to update all the sample calls on this page :<br/><br/>_Your API Host Base URL_<br/><input class="code-replace" data-item="http://bbk-coordinator:8001" data-name="base url" type="text" size="64" placeholder="enter url here">
{{% /alert %}}

{{% alert color="primary" %}}
All API calls in BitBroker require [authorisation](/docs/api-conventions/authorisation/). The sample calls below contain a placeholder string where you should insert your [coordinator API token](/docs/api-conventions/authorisation/#obtaining-a-coordinator-key). If you already have a token, enter it in the box below to update all the sample calls on this page:<br/><br/>_Your Coordinator API Token_<br/><input class="code-replace" data-item="your-token-goes-here" data-name="token" type="text" size="64" placeholder="enter token here">
{{% /alert %}}

## Creating a New Access

New accesses can be created by issuing an `HTTP/POST` to the `/user/:uid/access/:pid` end-point.

Accesses are always created within the context of a [user](/docs/concepts/users/) and a [policy](/docs/concepts/policy/). Hence, you must know the user ID (`uid`) and the policy ID (`pid`) in order to create one.

```shell
curl http://bbk-coordinator:8001/v1/user/2/access/over-a-billion \
     --request POST \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/policy/country
```

The body of this response will contain the access key, which the user should utilise [to authorise](/docs/api-conventions/authorisation/) their calls to the [Consumer API](/docs/consumer/). For example:

```
7b4cb14a-7227-4be2-80a8-4550b841b4f4.2edbb472-f278-4a0d-91db-9d21ada03c77.037d5a90-a26b-4b9f-ab57-57d4309d487c
```

{{% alert color="primary" %}}
It is expected that coordinator user will _securely_ distribute this consumer key to the user.
{{% /alert %}}

## Reissuing an Access

Existing accesses can be reissued by issuing an `HTTP/PUT` to the `/user/:uid/access/:pid` end-point.

Accesses are always created within the context of a [user](/docs/concepts/users/) and a [policy](/docs/concepts/policy/). Hence, you must know the user ID (`uid`) and the policy ID (`pid`) in order to reissue one.


```shell
curl http://bbk-coordinator:8001/v1/user/2/access/over-a-billion \
     --request PUT \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

The body of this response will contain the _new_ access key, which the user should utilise [to authorise](/docs/api-conventions/authorisation/) their calls to the [Consumer API](/docs/consumer/). For example:

```
2f5ddf4b-e1cd-484f-a218-fdc4b27c5c02.ed46b1eb-01b8-46de-868a-f08a99416716.4f466f48-3e9b-4aa6-a94f-e0aab49d9b94
```

{{% alert color="primary" %}}
It is expected that coordinator user will _securely_ distribute this new consumer key to the user.
{{% /alert %}}

{{% alert color="warning" %}}
Reissuing a new access key will invalidate the previous (now old) access key.
{{% /alert %}}

## List of Accesses

You can obtain a list of all the existing accesses a user has by issuing an `HTTP/GET` to the `/user/:uid/access` end-point.

Accesses are always created within the context of a [user](/docs/concepts/users/) and, hence, you must know the user ID (`uid`) to list their accesses.

```shell
curl http://bbk-coordinator:8001/v1/user/2/access \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return a JSON array as follows:

```js
[
    {
        "id": "over-a-billion",
        "url": "http://bbk-coordinator:8001/v1/user/2/access/over-a-billion",
        "policy": {
            "id": "over-a-billion",
            "url": "http://bbk-coordinator:8001/v1/policy/over-a-billion"
        },
        "created": "2022-06-01T14:18:30.635Z"
    }
    // ... other accesses here
]
```

Each access the user has will be returned within this array. Note: There is currently no paging on this API.

A shorter list of accesses by user can also be obtained when you ask for the [details of an individual user](/docs/coordinator/user/#details-of-an-existing-user). For example:

```shell
curl http://bbk-coordinator:8001/v1/user/2 \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return a JSON array as follows:

```js
{
    "id": 2,
    "url": "http://bbk-coordinator:8001/v1/user/2",
    "name": "Alice",
    "email": "alice@domain.com",
    "coordinator": false,
    "accesses": [
        {
            "id": "over-a-billion",
            "url": "http://bbk-coordinator:8001/v1/user/2/access/over-a-billion"
        }
        // ... other accesses here
    ],
    "addendum": {}
}
```

## Details of an Existing Access

You can obtain the details of an existing access by issuing an `HTTP/GET` to the `/user/:uid/access/:pid` end-point.

Accesses are always created within the context of a [user](/docs/concepts/users/) and a [policy](/docs/concepts/policy/). Hence, you must know the user ID (`uid`) and the policy ID (`pid`) to get it's details.

```shell
curl http://bbk-coordinator:8001/v1/user/2/access/over-a-billion \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return a JSON object as follows:

```js
{
    "id": "over-a-billion",
    "url": "http://bbk-coordinator:8001/v1/user/2/access/over-a-billion",
    "policy": {
        "id": "over-a-billion",
        "url": "http://bbk-coordinator:8001/v1/policy/over-a-billion"
    },
    "created": "2022-06-01T14:18:30.635Z"
}
```

{{% alert color="info" %}}
[Consumer keys](/docs/api-conventions/authorisation/#obtaining-a-consumer-key) are not stored in an accessible way within the system and hence not present in this returned document.
{{% /alert %}}

## Deleting an Access

Existing accesses can be deleted from the system by issuing an `HTTP/DELETE` to the `/user/:uid/access/:pid` end-point.

Accesses are always created within the context of a [user](/docs/concepts/users/) and a [policy](/docs/concepts/policy/). Hence, you must know the user ID (`uid`) and the policy ID (`pid`) to delete one.

```shell
curl http://bbk-coordinator:8001/v1/user/2/access/over-a-billion \
     --request DELETE \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Once the access is deleted the key issued with it will no longer return any results for any of the end-points which form the [Consumer API](/docs/consumer/).
{{% /alert %}}
