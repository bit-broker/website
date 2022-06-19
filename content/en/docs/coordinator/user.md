---
title: "Users"
linkTitle: "Users"
weight: 1
description: APIs for creating and manipulating users
---

Users are a main component of the BitBroker system. You will find [more details about users](/docs/concepts/users/), within the [key concepts](/docs/concepts/) section of this documentation.

{{% alert color="info" %}}
Every fresh install of BitBroker comes with one preinstalled user (`uid: 1`). This user is automatically created when the system is bought-up for the first time.
{{% /alert %}}

{{% alert color="primary" %}}
In our sample calls, we use the standard [server name and port](/docs/getting-started/install-local/#server-naming-and-ports) format outlined for [local installations](/docs/getting-started/install-local/) and [demos](/docs/getting-started/demo/). If you have an alternative API host base URL, then enter it in the box below to update all the sample calls on this page :<br/><br/>_Your API Host Base URL_<br/><input class="code-replace" data-item="http://bbk-coordinator:8001" data-name="base url" type="text" placeholder="enter url here">
{{% /alert %}}

{{% alert color="primary" %}}
All API calls in BitBroker require [authorisation](/docs/api-conventions/authorisation/). The sample calls below contain a placeholder string where you should insert your [coordinator API token](/docs/api-conventions/authorisation/#obtaining-a-coordinator-key). If you already have a token, enter it in the box below to update all the sample calls on this page:<br/><br/>_Your Coordinator API Token_<br/><input class="code-replace" data-item="your-token-goes-here" data-name="token" type="text" placeholder="enter token here">
{{% /alert %}}

## Creating a New User

New users can be created by issuing an `HTTP/POST` to the `/user` end-point.

```shell
curl http://bbk-coordinator:8001/v1/user \
     --request POST \
     --include \
     --header "Content-Type: application/json" \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-binary @- << EOF
     { \
         "name": "Alice", \
         "email": "alice@domain.com" \
     }
EOF
```

This will result in a response as follows:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/user/2
```

The ID for the new user will be returned within the `Location` attribute of the response header.

The following validation rules will be applied for the body of a new user request.

Attribute | Necessity | Validation Rules
--- | --- | ---
`name` | <div class="stamp">required</div> | String between 1 and 64 characters long
`email` | <div class="stamp">required</div> | String between 1 and 256 characters long<br/>Must conform to email address format<br/>Must be unique across all users in the system

{{% alert color="info" %}}
Email addresses need to be unique across an operating BitBroker instance. If you attempt to create a user with a duplicate email address, it will result in an `HTTP/1.1 409 Conflict` response.
{{% /alert %}}

New users will be present in the system right away. They can immediately take part as data consumers by being [assigned policy-based, consumer keys](/docs/api-conventions/authorisation/#obtaining-a-consumer-key). However, [a further step](#promoting-a-user-to-coordinator) is required to promote them to have coordinator status.

## Updating a User

Existing users can have their profile updated by issuing an `HTTP/PUT` to the `/user/:uid` end-point.

In order to update a user, you must know their user ID (`uid`).

```shell
curl http://bbk-coordinator:8001/v1/user/2 \
     --request PUT \
     --include \
     --header "Content-Type: application/json" \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-binary @- << EOF
     { \
         "name": "Bob" \
     }
EOF
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
You cannot update the user's email address, which was specified when they were created. Email addresses are system-wide unique identifiers. If you need to update an email address, your only option is to create a new user with that address.
{{% /alert %}}

The validation rules for updated user information, are the same as that for [creating new users](#creating-a-new-user).

## List of Existing Users

You can obtain a list of all the existing users by issuing an `HTTP/GET` to the `/user` end-point.

```shell
curl http://bbk-coordinator:8001/v1/user \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return a JSON array as follows:

```js
[
    {
        "id": 1,
        "url": "http://bbk-coordinator:8001/v1/user/1",
        "name": "Admin",
        "email": "noreply@bit-broker.io",
        "coordinator": true,
        "accesses": []
    },
    {
        "id": 2,
        "url": "http://bbk-coordinator:8001/v1/user/2",
        "name": "Bob",
        "email": "alice@domain.com",
        "coordinator": false,
        "accesses": []
    }
    // ... other users here
]
```

Each user on the system will be returned within this array. Later sections of this document will explain what the [`coordinator`](#promoting-a-user-to-coordinator) and [`accesses`](/docs/coordinator/access/) attributes refer to. Note: There is currently no paging on this API.

## Details of an Existing User

You can obtain the details of an existing user by issuing an `HTTP/GET` to the `/user/:uid` end-point.

In order to obtain details of a user, you must know their user ID (`uid`).

```shell
curl http://bbk-coordinator:8001/v1/user/2 \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return a JSON object as follows:

```js
{
    "id": 2,
    "url": "http://bbk-coordinator:8001/v1/user/2",
    "name": "Bob",
    "email": "alice@domain.com",
    "coordinator": false,
    "accesses": [],
    "addendum": {}
}
```

Later sections of this document will explain what the [`coordinator`](#promoting-a-user-to-coordinator), [`accesses`](/docs/coordinator/access/) and [`addendum`](#user-addendum-information)attributes refer to.

## Finding a User via Email Address

You can find an existing user via their email address by issuing an `HTTP/GET` to the `/user/email/:email` end-point.

In order to obtain details of a user, you must know their current `email` address.

```shell
curl http://bbk-coordinator:8001/v1/user/email/alice%40domain.com \
     --header "x-bbk-auth-token: your-token-goes-here"
```

{{% alert color="info" %}}
For best practice, the email address within this RESTful URL should be [url encoded](https://www.w3schools.com/tags/ref_urlencode.asp).
{{% /alert %}}

This will return a JSON object as follows:

```js
{
    "id": 2,
    "url": "http://bbk-coordinator:8001/v1/user/2",
    "name": "Bob",
    "email": "alice@domain.com",
    "coordinator": false,
    "accesses": [],
    "addendum": {}
}
```

## Promoting a User to Coordinator

Existing users can be promoted to assign them [coordinator status](/docs/concepts/users/#coordinators) by issuing an `HTTP/POST` to the `/user/:uid/coordinator` end-point.

In order to promote a user, you must know their user ID (`uid`).

```shell
curl http://bbk-coordinator:8001/v1/user/2/coordinator \
     --request POST \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 200 OK
```

The body of this response will contain the coordinator key, which the newly promoted user should utilise [to authorise](/docs/api-conventions/authorisation/) their own calls to the Coordinator API. For example:

```
4735d360-dc03-499c-91ce-68dfc1554691.5d5c9eab-1d9c-4c88-9478-9f4ab35568a7.423c9101-315a-4929-a64c-9b905837799c
```

{{% alert color="info" %}}
It is expected that _promoting_ user will _securely_ distribute this coordinator key to the _promoted_ user.
{{% /alert %}}

 Promoted users will gain coordinator privileges right away. When getting details for such users, their coordinator status will be reflected in the `coordinator` attribute:

```js
{
    "id": 2,
    "url": "http://bbk-coordinator:8001/v1/user/2",
    "name": "Bob",
    "email": "alice@domain.com",
    "coordinator": true,  // this is the new coordinator status
    "accesses": [],
    "addendum": {}
}
```

{{% alert color="info" %}}
If you attempt to promote a user who is _already_ a coordinator, it will result in an `HTTP/1.1 409 Conflict` response. This error is benign and will not impact the user's status.
{{% /alert %}}

## Demoting a User from Coordinator

Existing users can be demoted from [coordinator status](/docs/concepts/users/#coordinators) by issuing an `HTTP/DELETE` to the `/user/:uid/coordinator` end-point.

In order to demote a user, you must know their user ID (`uid`).

```shell
curl http://bbk-coordinator:8001/v1/user/2/coordinator \
     --request DELETE \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

Demoted users will lose coordinator privileges right away. The coordinator key they were holding will no longer be valid. When getting details for such users, their coordinator status will be reflected in the `coordinator` attribute:

```js
{
    "id": 2,
    "url": "http://bbk-coordinator:8001/v1/user/2",
    "name": "Bob",
    "email": "alice@domain.com",
    "coordinator": false,  // this is the new coordinator status
    "accesses": [],
    "addendum": {}
}
```

{{% alert color="info" %}}
If you attempt to demote a user who is _not_ a coordinator, it will result in an `HTTP/1.1 409 Conflict` response. This error is benign and will not impact the user's status.
{{% /alert %}}

{{% alert color="info" %}}
Demoting a user has no impact on any policy keys which they maybe holding for the [Consumer API](/docs/consumer/).
{{% /alert %}}

{{% alert color="warning" %}}
You cannot demote yourself from being a coordinator. If you attempt this, the system will return an `HTTP/1.1 405 Method Not Allowed` response. This is done in order to prevent a situation where there are no coordinators present within an operating instance. If you wish to demote yourself, you will need to ask _another coordinator_ to perform this action on your behalf.
{{% /alert %}}

## Deleting a User

Existing users can be deleted from the system by issuing an `HTTP/DELETE` to the `/user/:uid` end-point.

In order to delete a user, you must know their user ID (`uid`).

```shell
curl http://bbk-coordinator:8001/v1/user/2 \
     --request DELETE \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 204 No Content
```

{{% alert color="info" %}}
Any policy keys which were assigned to a deleted user will be rescinded as part of the deletion process. [Consumer API](/docs/consumer/) requests requests made with such keys will fail (after a short propagation delay).
{{% /alert %}}

{{% alert color="warning" %}}
You cannot delete yourself from the system. If you attempt this, the system will return an `HTTP/1.1 405 Method Not Allowed` response. This is done in order to prevent a situation where there are no users present within an operating instance. If you wish to delete yourself, you will need to ask _another coordinator_ to perform this action on your behalf.
{{% /alert %}}

## User Addendum Information

Observant readers will have noticed an `addendum` section at the bottom of the [user details](#details-of-an-existing-user) object:

```js
{
    "id": 2,
    "url": "http://bbk-coordinator:8001/v1/user/2",
    "name": "Bob",
    "email": "alice@domain.com",
    "coordinator": false,  
    "accesses": [],
    "addendum": {}  // this is the addendum section
}
```

This section is _reserved_ for use by the up-coming __BitBroker Portal__. We request that you do not use this section at this time.
