---
title: "User Data Access"
linkTitle: "Access"
weight: 5
description: APIs for creating and managing user's data access
---

Data access is a main component of the BitBroker system. You will find [more details about data access](todo), within the [key concepts](todo) section of this documentation.

Accesses are always created within the context of a [user](todo) and a [policy](todo). These can be created and manipulated using [other APIs](todo) described earlier in this documentation.

{{% alert color="primary" %}}
In order to use the sample calls in this section, first [user](todo) and the [policy](todo) as outlined in the previous sections.
{{% /alert %}}

Before you use this API, you should become familiar with the general, system-wide [API principles](todo) - which are used across all three BitBroker API sets. These principles include topics such as [standard logical server names and ports](todo), [RESTful designs](todo), [authorisation](todo), [error handling](todo), etc.

## Creating a New Access

New accesses can be created by issuing an `HTTP/POST` to the `/user/:uid/access/:pid` end-point. Accesses are always created within the context of a user and a policy. Hence, you must know the user ID (uid) and the policy ID (pid) in order to create one.

```shell
curl http://bbk-coordinator:8001/v1/user/2/access/over-a-billion \
     --request POST \
     --include
```

This will result in:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/policy/country
```

The body of this response will contain the access key, which the user should utilise [to authorise](todo) their calls to the [Consumer API](todo). For example:

```
7b4cb14a-7227-4be2-80a8-4550b841b4f4.2edbb472-f278-4a0d-91db-9d21ada03c77.037d5a90-a26b-4b9f-ab57-57d4309d487c
```

It is expected that coordinator user will distribute this consumer key to the user.

## Reissuing an Access

Existing accesses can be reissued by issuing an `HTTP/PUT` to the `/user/:uid/access/:pid` end-point. You must know the user ID (uid) and the policy ID (pid) in order to reissue one.


```shell
curl http://bbk-coordinator:8001/v1/user/2/access/over-a-billion \
     --request PUT \
     --include
```

This will result in:

```
HTTP/1.1 204 No Content
```

The body of this response will contain the _new_ access key, which the user should utilise [to authorise](todo) their calls to the [Consumer API](todo). For example:

```
2f5ddf4b-e1cd-484f-a218-fdc4b27c5c02.ed46b1eb-01b8-46de-868a-f08a99416716.4f466f48-3e9b-4aa6-a94f-e0aab49d9b94
```

It is expected that coordinator user will distribute this new consumer key to the user.


{{% alert color="warning" %}}
Reissuing a new access key will invalidate the previous (now old) access key.
{{% /alert %}}

## List of Accesses

You can obtain a list of all the existing accesses a user has by issuing an `HTTP/GET` to the `/user/:uid/access` end-point. Accesses are always created within the context of a user and, hence, you must know the user ID (uid) to list their accesses.

```shell
curl http://bbk-coordinator:8001/v1/user/2/access
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

A shorter list of access by user can also be obtained when you ask for the [details of an individual user](todo). For example:

```shell
curl http://bbk-coordinator:8001/v1/user/2
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

You can obtain the details of an existing access by issuing an `HTTP/GET` to the `/user/:uid/access/:pid` end-point. Accesses are always created within the context of a user and a policy. Hence, you must know the user ID (uid) and the policy ID (pid) to get it's details.

```shell
curl http://bbk-coordinator:8001/v1/user/2/access/over-a-billion
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

## Deleting an Access

Existing accesses can be deleted from the system by issuing an `HTTP/DELETE` to the `/user/:uid/access/:pid` end-point. Accesses are always created within the context of a user and a policy. Hence, you must know the user ID (uid) and the policy ID (pid) to delete one.

```shell
curl --request DELETE \
     --include \
     http://bbk-coordinator:8001/v1/user/2/access/over-a-billion
```

This will result in:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
Once the access is deleted the key issued with it will no longer return any results for any of the end-points which form the [Consumer API](todo).
{{% /alert %}}
