---
title: "Structure and Format"
linkTitle: "Structure"
weight: 1
description: How the APIs are structured and formatted
---

### RESTful Architecture

All the API sets within BitBroker confirm to the common standards for a [RESTful API](https://en.wikipedia.org/wiki/Representational_state_transfer). There are many resources around the web to learn and understand what RESTful APIs are and how to work with them. If you are unfamiliar with this API architecture, we encourage you to investigate it further before you use the BitBroker APIs.

#### Resource Manipulation

RESTful APIs use HTTP concepts to access and manipulate resources on the hosting server. Typical actions are to Create, Update and Delete resources. As with the standard convention, BitBroker maps HTTP methods to resource actions as follows:

HTTP Method | Resource Action
--- | ---
`HTTP/GET` | Read a resource or a list of resources
`HTTP/POST` | Create a new resource
`HTTP/PUT` | Update an existing resource
`HTTP/DELETE` | Delete an existing resource

#### Data Exchange

All data exchange, both to and from the server, is in [JavaScript Object Notation (JSON)](https://www.json.org/json-en.html) format. When posting data to the server as part of a Create or Update action, documents must be valid JSON and all JSON keys should be double-quoted.  When posting data, the HTTP header "Content-Type" should be set to "application/json".

#### API Returns

RESTful APIs use HTTP response codes to indicate return status from the call. BitBroker uses a subset of HTTP response codes and maps then to call state as follows:

HTTP Response | API Call State
--- | ---
`HTTP/1.1 200 OK` | The request completed successfully and data is present in the response body
`HTTP/1.1 201 Created` | The requested resource was successfully created - _the new resources URI will be returned in the 'Location' attribute of the response header_
`HTTP/1.1 204 OK` | The request completed successfully, but there is no data in the response body
`HTTP/1.1 400 Bad Request` | The request was rejected because it resulted in validation errors - _for example, a mandatory attribute was not sent in the request_
`HTTP/1.1 401 Unauthorized` | The request was rejected because it contains an unapproved context - _for example, a supplied session id does not match that which was given earlier_
`HTTP/1.1 403 Forbidden` | The request was rejected because the caller does not sufficient permissions - _most often a failure of [authorisation](todo)_
`HTTP/1.1 404 Not Found` | The request was rejected because the resource is not present
`HTTP/1.1 405 Method Not Allowed` | The request was rejected because the action is not permitted - _for example, deleting yourself as a user_
`HTTP/1.1 409 Conflict` | The request was rejected because the action would cause a conflict on existing resource state - _for example, creating a policy with a duplicate id_
`HTTP/1.1 429 Too Many Requests` | The request was rejected because a limit has been exceeded - _for example, the call rate or call quota has been breached_

{{% alert color="warning" %}}
Responses of `HTTP/1.1 500 Server Error` should never be returned in normal operation. If you see such an error, do help us to identify and rectify the underlying problem. Please [raise an issue](todo) in our GitHub repo describing, in as much detail as possible, the circumstances which lead to this error.
{{% /alert %}}
