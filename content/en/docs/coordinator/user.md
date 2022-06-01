---
title: "Users"
linkTitle: "Users"
weight: 1
description: APIs for creating and manipulating users
---

Users are a main component of the BitBroker system. You will find [more details about users](todo), within the [key concepts](todo) section of this documentation.

Before you use this API, you should become familiar with the general, system-wide [API principles](todo) - which are used across all three BitBroker API sets. These principles include topics such as [standard logical server names and ports](todo), [RESTful designs](todo), [authorisation](todo), [error handling](todo), etc.

## Creating a New User

New users can be created by issuing an `HTTP/POST` to the `/user` end-point.

```shell
curl http://bbk-coordinator:8001/v1/user \
     --request POST \
     --include \
     --header "Content-Type: application/json" \
     --data-binary @- << EOF
     { \
         "name": "Alice", \
         "email": "alice@domain.com" \
     }
EOF
```

This will result in:

```
HTTP/1.1 201 Created
Location: http://bbk-coordinator:8001/v1/user/2
```

The ID for the new user will be returned within the `Location` attribute of the response header. The following validation rules exist for the body of a new user request.

Attribute | Necessity | Validation Rules
--- | --- | ---
`name` | <div class="stamp">required</div> | String between 1 and 64 characters long
`email` | <div class="stamp">required</div> | String between 1 and 256 characters long<br/>Must conform to email address format<br/>Must be unique across all users in the system

{{% alert color="info" %}}
Email addresses need to be unique across an operating BitBroker instance. If you attempt to create a user with a duplicate email address, this will result in an `HTTP/1.1 409 Conflict` response.
{{% /alert %}}

New users will be present in the system right away. They can immediately take part as data consumers by being [assigned policy-based data access keys](todo). However, [a further step](todo) is required to promote them to have coordinator status.

## Updating a User

Existing users can have their profile updated by issuing an `HTTP/PUT` to the `/user/:uid` end-point. In order to update a user, you must know their user ID (uid).

```shell
curl http://bbk-coordinator:8001/v1/user/2 \
     --request PUT \
     --include \
     --header "Content-Type: application/json" \
     --data-binary @- << EOF
     { \
         "name": "Bob" \
     }
EOF
```

This will result in:

```
HTTP/1.1 204 No Content
```

{{% alert color="warning" %}}
You cannot update the user's email address which was specified when they were created. Email addresses are system-wide unique identifiers. If you need to update an email address, your only option is to create a new user with that address.
{{% /alert %}}

The validation rules for updated user information, is the same as that for [creating new users](#creating-a-new-user).

## List of Existing Users

You can obtain a list of all the existing users by issuing an `HTTP/GET` to the `/user` end-point.

```shell
curl http://bbk-coordinator:8001/v1/user
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

Each user on the system will be returned within this array. Later sections of this document will explain what the [coordinator](#promoting-users-to-be-coordinators) and [accesses](todo) attributes refer to. Note: There is currently no paging on this API.

## Details of an Existing User

You can obtain the details of an existing user by issuing an `HTTP/GET` to the `/user/:uid` end-point. In order to obtain details of a user, you must know their user ID (uid).

```shell
curl http://bbk-coordinator:8001/v1/user/2
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

Later sections of this document will explain what the [coordinator](#promoting-users-to-be-coordinators), [accesses](todo) and [addendum](#user-addendum-information) attributes refer to.

## Finding a User via Email Address

You can find an existing user via their email address by issuing an `HTTP/GET` to the `/user/email/:email` end-point. In order to obtain details of a user, you must know their current email address.

```shell
curl http://bbk-coordinator:8001/v1/user/email/alice%40domain.com
```

{{% alert color="info" %}}
For best practice, the email address within this RESTful URL should be [url encode](https://www.w3schools.com/tags/ref_urlencode.asp).
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

Existing users can be promoted to assign them coordinator status by issuing an `HTTP/POST` to the `/user/:uid/coordinator` end-point. In order to promote a user, you must know their user ID (uid).

```shell
curl http://bbk-coordinator:8001/v1/user/2/coordinator \
     --request POST \
     --include
```

This will result in:

```
HTTP/1.1 200 OK
```

The body of this response will contain the coordinator key which the promoted user should utilise [to authorise](todo) their own calls to the Coordinator API. For example:

```
4735d360-dc03-499c-91ce-68dfc1554691.5d5c9eab-1d9c-4c88-9478-9f4ab35568a7.423c9101-315a-4929-a64c-9b905837799c
```

It is expected that _promoting_ user will distribute this coordinator key to the _promoted_ user. Promoted users will gain coordinator privileges right away. When getting details for such users, their coordinator status will be reflected in the corresponding attribute:

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
If you attempt to promote a user who is already a coordinator, this will result in an `HTTP/1.1 409 Conflict`. This error is benign and will not impact the user status.
{{% /alert %}}

## Demoting a User from Coordinator

Existing users can be demoted from coordinator status by issuing an `HTTP/DELETE` to the `/user/:uid/coordinator` end-point. In order to demote a user, you must know their user ID (uid).

```shell
curl http://bbk-coordinator:8001/v1/user/2/coordinator \
     --request DELETE \
     --include
```

This will result in:

```
HTTP/1.1 204 No Content
```

Promoted users will lose coordinator privileges right away. The coordinator key they were holding will no longer be valid. When getting details for such users, their coordinator status will be reflected in the corresponding attribute:

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
If you attempt to demote a user who is not a coordinator, this will result in an `HTTP/1.1 409 Conflict`. This error is benign and will not impact the user status.
{{% /alert %}}

{{% alert color="info" %}}
Demoting a user has no impact on any policy keys which they maybe holding for the [Consumer API](todo).
{{% /alert %}}

{{% alert color="warning" %}}
You cannot demote yourself from being a coordinator. If you attempt this, the system will return `HTTP/1.1 405 Method Not Allowed`. This is done in order to prevent a situation where there are no coordinators present within an operating instance.
{{% /alert %}}

## Deleting a User

Existing users can be deleted from the system by issuing an `HTTP/DELETE` to the `/user/:uid` end-point. In order to delete a user, you must know their user ID (uid).

```shell
curl http://bbk-coordinator:8001/v1/user/2 \
     --request DELETE \
     --include
```

This will result in:

```
HTTP/1.1 204 No Content
```

{{% alert color="info" %}}
Any policy keys which were assigned to a deleted user will be rescinded as part of the deletion process. [Consumer API requests](todo) requests made with such keys will fail, after a short propagation delay.
{{% /alert %}}

{{% alert color="warning" %}}
You cannot delete yourself from the system. If you attempt this, the system will return `HTTP/1.1 405 Method Not Allowed`. This is done in order to prevent a situation where there are no users present within an operating instance.
{{% /alert %}}

## User Addendum information

Observant readers will have noted an addendum section at the bottom of the user details object:

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

This section is _reserved_ for use by the up-coming BitBroker Portal. We ask that you do not use this section at this time.
