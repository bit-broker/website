---
title: "Contributing Records"
linkTitle: "Submitting Records"
weight: 1
description: How connectors contribute entity instance records to BitBroker
---

All the data being managed by a BitBroker instance, enters the system via the [Contribution API](/docs/contributor/). The process of contributing such data is documented in detail in this section.

In this section, we will consider the basic use case of contributing entity instance records. Later sections of this documentation will detail how you can contribute [live, on-demand data](/docs/contributor/webhooks/#entity-end-point) and [timeseries data](/docs/contributor/webhooks/#timeseries-end-point).

{{% alert color="primary" %}}
Contributing data is tightly bound with the concepts of [entity types](/docs/concepts/entity-types/) and their associated [data connectors](/docs/concepts/connectors/). All contributions happen in the context of these important system elements. It is vital that you fully understand these and other [key concepts](/docs/concepts/) before using this API to contribute records.
{{% /alert %}}

{{% alert color="primary" %}}
A quick way to get going building your own data connectors is to adapt the [example connectors](/docs/examples/connectors/) which have been built for a range of data sources.
{{% /alert %}}

{{% alert color="primary" %}}
All API calls in BitBroker require [authorization](/docs/api-conventions/authorization/). The sample calls below contain a placeholder string where you should insert your [contributor API token](/docs/api-conventions/authorization/#obtaining-a-contributor-token). This key should have been provided to you by the coordinator user who created your data connector within BitBroker.
{{% /alert %}}

{{% alert color="primary" %}}
The sample calls in this section will not work as-is. Contributor API calls require the use of session IDs, which are generated on-demand. Hence, the sample calls here are merely illustrative.
{{% /alert %}}

## Contributing Records to the Catalog

We will assume for the purposes of this section that an [entity type](/docs/concepts/entity-types/) and it's associated [data connector](/docs/concepts/connectors/) have been created and are present within the system. Further, that the connector ID and key, which were obtained when the [data connecter was created](/docs/coordinator/connectors/#creating-a-new-connector), have been recorded and are available.

Data can now be contributed into [the catalog](/docs/concepts/catalog/) by this data connector, but within the context of its parent entity type only. Hence, we say that a single connector contributes "entity instance records". If one organization wants to contribute data to multiple entity types, then they must do this via multiple data connectors.

The process of contributing entity instance records into [the catalog](/docs/concepts/catalog/) breaks down into three steps:

1. Create a data contribution session
1. Upsert and/or delete records into this session
1. Close the session

These steps are achieved via an HTTP based API, which we outline in detail below. Each data connector will have a private end-point on this API which is waiting for its contributions.

{{% alert color="primary" %}}
It is important to understand the distinction between _data_ and _metadata_ in the context of the BitBroker instance. It is an expectation that only metadata is being contributed into the catalog and that live data is kept back for on-demand requests. This distinction is [outlined in more detail](/docs/concepts/catalog/#data-vs-metadata) in the key concepts documentation.
{{% /alert %}}

{{% alert color="primary" %}}
It is important to understand that data connectors might be in a _live_ or _staged_ state. That is, their contribution might be approved for the live catalog, or might be being held back into a staging space only. This concept is [outlined in more detail](/docs/concepts/connectors/#live-vs-staging-connectors) in the key concepts documentation. There is a [mechanism available](/docs/consumer/#accessing-staged-records) in the [Consumer API](/docs/consumer/) which allows data connectors to see how their records will look alongside other existing public records.
{{% /alert %}}

{{% alert color="info" %}}
If your connector is marked as "non-live", your data contribution will _not_ become visible to [consumers](/docs/concepts/users/#consumers). If you want to make your connector "live", you must ask the coordinator user who created the connector for you.
{{% /alert %}}

### Sessions

Sessions are used by the Contribution API to manage inbound data coming from the community of data connectors. Sessions allow the connectors to contribute entity instance records in well-defined ways, which are respectful of the state management of the source data store.

BitBroker supports three types of sessions: _stream_, _accrue_ and _replace_. Each one provides for different update and delete contexts.

{{% alert color="warning" %}}
You can only have one session open at a time. If you open a new session without closing a previous one, the previous one is implicitly closed with a rollback request.
{{% /alert %}}

The three types of session provide for different application logic in the following areas:

* Whether data is available to [consumers](/docs/concepts/users/#consumers) whilst the session is still open or only after is it closed.
* Whether the data provided within a session _adds to_ or _replaces_ earlier data from your connector.

Here is the detail of how each session type functions:

Area | Stream | Accrue | Replace
--- | --- | --- | ---
Data visibility | as soon as posted | on session close | on session close
Data from previous session | in addition to | in addition to | replaces entirely

Let's explore each of these in more detail:

#### Stream Sessions

Stream sessions are likely to be the default mode of operation for most data connectors. Inbound entity instance records arrive in the catalog as soon as they are posted and whilst the session remains open. They are immediately available to consumers to view via the [Consumer API](/docs/consumer/).

New records are in addition to existing records in the catalog and removal must be explicitly requested. Closing a stream session is a moot operation, since the session type is essentially an "open pipe" into the catalog. In fact, stream sessions can be opened and left open indefinitely.

 Type | Session  | Action
 --- | --- | ---
stream | <div class="stamp">open</div>  | session data is already visible, in addition to previous data
stream | <div class="stamp">close<br/>`true`</div>  | no operation - session data is already visible, in addition to previous data
stream | <div class="stamp">close<br/>`false`</div> | no operation - session data is already visible, in addition to previous data

#### Accrue Sessions

Accrue sessions are useful when entity instance records should only become visible as complete sets. In this scenario, the entity instance records contributed within a session, only become visible via the [Consumer API](/docs/consumer/) when the session is closed - and hence only as a complete set.

New records are in addition to existing records in the catalog and removal must be explicitly requested. When you close an accrue session, you must specify a commit state as `true` or `false`. Closing the session with `true` makes the contributed records visible in the Consumer API, but closing it with `false` will discard all the records contributed within that session.

 Type | Close  | Action
 --- | --- | ---
 accrue | <div class="stamp">open</div>  | session data not visible, but previous data is
 accrue | <div class="stamp">close<br/>`true`</div> | session data now becomes visible, in addition to previous data
 accrue | <div class="stamp">close<br/>`false`</div> | session data is discarded and previous data persists

#### Replace Sessions

Replace sessions are useful when contributed entity instance records should completely replace the set provided in previous sessions. In this scenario, the entity instance records contributed within a session,  become visible via the [Consumer API](/docs/consumer/) when the session is closed as a complete set - but all the records contributed in earlier sessions are discarded. Replace sessions are useful when you cannot maintain state about earlier contributions, and hence each contribution is a complete statement of your record set.

New records are in replacement for existing records in the catalog and removal of these "old" records is implicit. When you close an accrue session, you must specify a commit state as `true` or `false`. Closing the session with `true` makes the contributed records visible in the Consumer API and deletes records from previous sessions. However, closing it with `false` will discard all the records contributed within that session and previously contributed records will remain untouched.

 Type | Close  | Action
 --- | --- | ---
replace | <div class="stamp">open</div>  | session data not visible, but previous data is
replace | <div class="stamp">close<br/>`true`</div> | session data now becomes visible and replaces _all_ previous data
replace | <div class="stamp">close<br/>`false`</div> | session data is discarded and previous data persists

As you can see, picking the right session type is vitally important to ensure you make the best use of the catalog. In general, you should aim to use a `stream` type session where you can, as this is the simplest.

If you don't want clients to be able to see intermediate updates in the catalog, then `accrue` and `replace` may be better options. Where you don't want to (or can't) store any state about what you previously sent to the catalog, then `replace` is probably the best option.

### Using Sessions

There are only three HTTP calls which your data connectors need make in order to contribute records into the catalog.

#### Opening a Session

New sessions can be created by issuing an `HTTP/GET` to the `/connector/:cid/session/open/:mode` end-point.

In order to open a session, you must know the connector ID (`cid`). This should have been communicated to you by the coordinator user who created your data connector within BitBroker.

You will also need to select one of the three session `modes` from `stream`, `accure` and `replace`. These should be specified in lowercase and without any spaces.

```shell
curl http://bbk-contributor:8002/v1/connector/9afcf3235500836c6fcd9e82110dbc05ffbb734b/session/open/stream \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 200 OK
```

The body of this response will contain a session ID (`sid`), which should be recorded as it will be needed for subsequent API calls. For example:

```
4527eff4-d9cf-41c0-9ecc-8e06b57fcf54
```

#### Posting Records in a Session

Once you have an open session, you can post two types of actions to it in order to manipulate your catalog entries.

* `upsert` to _update_ or _insert_ a record into the catalog
* `delete` to _remove_ an existing record from the catalog

{{% alert color="info" %}}
You can only make changes to your own records within the catalog. Your data connector will have no effect on records which came from other connectors - even if you share an entity type with them.
{{% /alert %}}

Entity instance records can be upserted or deleted by issuing an `HTTP/POST` to the `/connector/:cid/session/:sid/:action` end-point.

In order to post record actions, you must know the connector ID (`cid`). This should have been communicated to you by the coordinator user who created your data connector within BitBroker. You must also know the session ID (`sid`), which was returned in the previous step where a [session was opened](#opening-a-session).

Finally, you will also need to select one of the two valid `actions` from `upsert` and `delete`. These should be specified in lowercase and without any spaces.

```shell
curl http://bbk-contributor:8002/v1/connector/9afcf3235500836c6fcd9e82110dbc05ffbb734b/session/4527eff4-d9cf-41c0-9ecc-8e06b57fcf54/upsert \
     --request POST \
     --include \
     --header "Content-Type: application/json" \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-binary @- << EOF
     [ ]
EOF
```

{{% alert color="info" %}}
You can specify upsert and/or delete record actions, but these cannot be mixed into a single API call. However, you can upsert and delete as many times as you wish within an open session.
{{% /alert %}}

{{% alert color="info" %}}
Your upsert and delete actions will be executed in the strict order in which they were sent. You can safely upsert and then delete an entity instance within a single session boundary, if you so wish.
{{% /alert %}}

{{% alert color="warning" %}}
Care should be taken to ensure that the session ID (`sid`) used to post updates is the ID which was returned in the last call to [open a session](#opening-a-session). If you send an old, invalid or mismatched session ID, it will result in an `HTTP/1.1 403 Forbidden` response. This will have no impact on any existing open session.
{{% /alert %}}

In the example above, we upsert an empty array - this is obviously not useful. Let's now look in detail about how records are inserted, update and deleted using this API call.

##### Upserting records

When you post an upsert request, you should include an array of entity instances in JSON format within your post body. Each record can contain the following attributes:

Attribute | Necessity | Validation Rules
--- | --- | ---
`id` | <div class="stamp">required</div> | String between 1 and 64 characters long
`name` | <div class="stamp">required</div> | String between 1 and 64 characters long
`entity` | <div class="stamp">required</div> | An object conforming to the entity schema for this entity type
`instance` | <div class="stamp">optional</div> | An object containing other, ancillary information

{{% alert color="info" %}}
Only expected attributes will be stored within the catalog. Any other attributes which are sent will simply be ignored.
{{% /alert %}}

It is important to understand the difference between the three classes of attributes which you can be present within each entity instance record:

###### Global Attributes

These attributes are required to be present for entity instance in the system, regardless of its entity type. This set consists of only these attributes:

Attribute | Description
--- | ---
`id` | Your domain key for this entity instance
`name` | A human-readable name describing this entity instance

###### Entity Attributes

These attributes are required to be present for entity instance in the system, of a given entity type. This set of attributes will have been communicated to you by the coordinator user who created your connector within BitBroker. It will presented in the form of a [JSON schema](https://json-schema.org/).

###### Instance Attributes

These attributes only exist for a given entity instance in the system. This is a free format object which can be used to store additional or ancillary information.

This simple hierarchy of three classes (`global`, `entity` and `instance`) is designed to give [consumers](/docs/concepts/users/#consumers) maximum assurance about which data can be expected to be available to them:

* They can _always_ expect to find the `global` data present
* They have firm expectations about data availability _within_ an `entity` type
* They understand that `instance` data is _ad-hoc_ and cannot be relied upon

{{% alert color="warning" %}}
If any record within the posted record set contains a validation error, then the entire set will be rejected. The call will return an `HTTP/1.1 400 Bad Request` response and the body of the response will contain details of every record validation error which was encountered in the [standard validation error format](/docs/api-conventions/errors/#validation-error-format).
{{% /alert %}}

{{% alert color="info" %}}
The catalog will decide whether to insert or update your record based upon the domain key which you supplied in the `id` field of each posted record. If a record already exists with this key, it will be updated - otherwise it will be inserted.
{{% /alert %}}

{{% alert color="info" %}}
Your records are scoped to be within your data connector space only. You cannot affect records delivered by another data connector, even within the same entity type and even if you have a clashing key space.
{{% /alert %}}

Here is the post body for an example upsert request for a set of three records:

```js
[
    {
        "id": "GB",
        "name": "United Kingdom",
        "entity": {
            "area": 242900,
            "calling_code": 44,
            "capital": "London",
            "code": "GB",
            "continent": "Europe",
            "currency": {
                "code": "GBP",
                "name": "Sterling"
            },
            "population": 66040229
        }
    },
    {
        "id": "IN",
        "name": "India",
        "entity": {
            "area": 3287263,
            "calling_code": 91,
            "capital": "New Delhi",
            "code": "IN",
            "continent": "Asia",
            "currency": {
                "code": "INR",
                "name": "Indian Rupee"
            },
            "population": 1344860000
        },
        "instance": {
            "independence": 1947
        }
    },
    {
        "id": "BR",
        "name": "Brazil",
        "entity": {
            "area": 8547403,
            "calling_code": 55,
            "capital": "Brasilia",
            "code": "BR",
            "continent": "South America",
            "currency": {
                "code": "BRL",
                "name": "Brazilian Real"
            },
            "population": 209659000
        },
        "instance": {}
    }
]
```

Whenever records are upserted into the catalog, it will return a report to the caller with information about how each posted record was processed. For example, for the three records above, you might get a report such as:

```js
{
    "GB": "5ebb30afaa6ce33843b00bbff63f63b90e91028c",
    "IN": "917d0311c687e5ffb28c91a9ea57cd3a306890d0",
    "BR": "d5fa7d9d8e4625399da7771fc0e3e87886f2a5ac"
}
```

In the report, you will see a row for every record that was posted, alongside the BitBroker key which is
being used for this entity instance. This is the key which [consumers](/docs/concepts/users/#consumers) will use in order to retrieve this record via the [Consumer API](/docs/consumer/).

{{% alert color="info" %}}
_There is no expectation that you need to store this consumer key_, if you do not wish to do so. You should continue to simply use your own domain key for your catalog interactions.
{{% /alert %}}

##### Deleting records

When deleting records from the catalog, you need to simply post an array of your domain keys for the records to be removed. These should be the same domain keys you specified when you upserted the records. For example, to remove two of the records upserted in the previous step, the post body would need to be:

```js
[ "GB", "BR" ]
```

Whenever records are deleted from the catalog, it will return a report to the caller with information about how each posted ID was processed. For example, for the two IDs above, you might get a report such as:

```js
{
    "GB": "5ebb30afaa6ce33843b00bbff63f63b90e91028c",
    "BR": "d5fa7d9d8e4625399da7771fc0e3e87886f2a5ac"
}
```

In the report, you will see a row for every ID that was posted, alongside the BitBroker key which was
being used for this (now removed) entity instance. This is the key which [consumers](/docs/concepts/users/#consumers) will have used in order to retrieve this record via the [Consumer API](/docs/consumer/).

{{% alert color="info" %}}
If you post a domain key to delete a record which does not exist in the catalog, this will simply be ignored.
{{% /alert %}}

#### Closing a Session

After entity instance records have been posted, you can be close a session by issuing an `HTTP/GET` to the `/connector/:cid/session/:sid/close/:commit` end-point.

In order to post record actions, you must know the connector ID (`cid`). This should have been communicated to you by the coordinator user who created your data connector within BitBroker. You must also know the session ID (`sid`), which was returned in the previous step where a [session was opened](#opening-a-session).

Finally, you will also need to select one of the two valid `commits` from `true` and `false`. These should be specified in lowercase and without any spaces.

```shell
curl http://bbk-contributor:8002/v1/connector/9afcf3235500836c6fcd9e82110dbc05ffbb734b/session/4527eff4-d9cf-41c0-9ecc-8e06b57fcf54/close/true \
     --include \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will result in a response as follows:

```
HTTP/1.1 200 OK
```

The exact mechanics of closing a session depends on the type of session that specified when it was opened. This was covered in detail in the earlier section on [session types](#sessions).
