---
title: "API Architecture"
linkTitle: "Architecture"
weight: 1
description: How the APIs are structured and formatted
---

All the API sets within BitBroker conform to the commonly accepted standards of a [RESTful API](https://en.wikipedia.org/wiki/Representational_state_transfer).

There are many resources around the web to learn and understand what RESTful APIs are and how to work with them. If you are unfamiliar with this API architecture, we encourage you to investigate it further before you use the BitBroker APIs.

#### Resource Manipulation

RESTful APIs use HTTP concepts to access and manipulate resources on the hosting server. Typical manipulations are to Create, Update and Delete resources. As with the standard RESTful convention, BitBroker maps HTTP methods to resource actions as follows:

HTTP Method | Resource Action
--- | ---
`HTTP/GET` | Read a resource or a list of resources
`HTTP/POST` | Create a new resource
`HTTP/PUT` | Update an existing resource
`HTTP/DELETE` | Delete an existing resource

#### Data Exchange

All data exchange with BitBroker APIs, both to and from the server, is in [JavaScript Object Notation](https://www.json.org/json-en.html) (JSON) format.

When posting data via an API (most often as part of a _create_ or _update_ action), the message body must be valid JSON document and all the JSON keys within it should be "double-quoted". If you are seeing validation errors indicating the JSON is incorrectly formed, you might want to try a [JSON validator](https://jsonlint.com/) to get more detailed validation information.

When posting data via an API, the HTTP header `Content-Type` should always be set to `application/json`.

#### API Responses

RESTful APIs use HTTP response codes to indicate the return status from the call. BitBroker uses a subset of the standard [HTTP response codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) and maps them to call state as follows:

HTTP Response | Type | API Call State
--- | --- | ---
`HTTP/1.1 200 OK` | <div class="stamp">success</div> | The request completed successfully and data is present in the response body
`HTTP/1.1 201 Created` | <div class="stamp">success</div> | The requested resource was successfully created - _the new resources URI will be returned in the `Location` attribute of the response header_
`HTTP/1.1 204 OK` | <div class="stamp">success</div> | The request completed successfully, but there is _no data_ in the response body
`HTTP/1.1 400 Bad Request` | <div class="stamp text-warning">error</div> | The request was rejected because it resulted in validation errors - _for example, a mandatory attribute was not sent in the request_
`HTTP/1.1 401 Unauthorized` | <div class="stamp text-warning">error</div> | The request was rejected because it contains an unapproved context - _for example, a supplied access key was not valid or has expired (most often a failure of [authorisation](/docs/api-conventions/authorisation/))_
`HTTP/1.1 403 Forbidden` | <div class="stamp text-warning">error</div> | The request was rejected because it contains an invalid or expired context - _for example, a supplied access key referring to a deleted policy_
`HTTP/1.1 404 Not Found` | <div class="stamp text-warning">error</div> | The request was rejected because the specified resource is not present
`HTTP/1.1 405 Method Not Allowed` | <div class="stamp text-warning">error</div> | The request was rejected because the action is not permitted - _for example, a user deleting themselves_
`HTTP/1.1 409 Conflict` | <div class="stamp text-warning">error</div> | The request was rejected because the action would cause a conflict on existing resource state - _for example, creating a policy with a duplicate id_
`HTTP/1.1 429 Too Many Requests` | <div class="stamp text-warning">error</div> | The request was rejected because a limit has been exceeded - _for example, the [policy defined](/docs/concepts/policy/) call rate or call quota has been breached_

Whenever you receive an error response (`HTTP/1.1 4**`), the response body will contain further information within a [standard error response](/docs/api-conventions/errors/) format.

{{% alert color="warning" %}}
Responses of `HTTP/1.1 500 Server Error` should never be returned in normal operation. If you see such an error, do help us to identify and rectify the underlying problem. Please [raise an issue](https://github.com/bit-broker/bit-broker/issues) in our GitHub repositary describing, in as much detail as possible, the circumstances which lead to the error.
{{% /alert %}}

#### API Versioning

As the system develops, there may be changes to the API structure to encompass new concepts or features. Where API modifications imply code changes for existing clients, a new version of the API will be released.

{{% alert color="info" %}}
For now, there is only one version of each of the API sets.
{{% /alert %}}

All the APIs within BitBroker include a version string as the lead resource. For example the `/v1/` part of the following:

```
http://bbk-coordinator:8001/v1/user
```

A version string _must be present_ in every API call in each API set.
