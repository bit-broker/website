---
title: "Error Handling"
linkTitle: "Errors"
weight: 3
description: How API errors are handled and communicated
---

All errors returned by all API services are presented in a standard error format. This complete list of errors which you may encounter are listed in the earlier page detailing the [API architecture](/docs/api-conventions/architecture/#api-responses).

#### Standard Error Format

All errors will be present in a common structure as follows:

```js
{
    "error": {
        "code": 404,
        "status": "Not Found",
        "message": ""
    }
}
```

In such circumstances, there will only ever be one `error` object with the following attributes:

Attribute | Presence | Description
--- | --- | ---
`code` | <div class="stamp">always</div> | The standard [HTTP response code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
`status` | <div class="stamp">always</div> | The standard text associated with the HTTP response code
`message` | <div class="stamp">always</div> | A string (sometimes) containing extra information about the error condition

#### Validation Error Format

In situations which lead to a validation error on inbound data, the response code will always be `HTTP/1.1 400 Bad Request`. In the case of such errors, the `message` attribute will always contain an array of validation errors:

```js
{
    "error": {
        "code": 400,
        "status": "Bad Request",
        "message": [
            {
                "name": "name",
                "index": null,
                "reason": "does not meet minimum length of 1"
            },
            {
                "name": "email",
                "index": null,
                "reason": "does not conform to the 'email' format"
            }
        ]
    }
}
```

Here, the message attributes will be as follows:

Attribute | Presence | Description
--- | --- | ---
`message` | <div class="stamp">always</div> | An array of validation error objects
`message.name` | <div class="stamp">always</div> | A string indicating the id of the erroring attribute - _this can be present multiple times, when there are multiple validation issues with the same attribute_
`message.index` | <div class="stamp">always</div> | An integer indicating the index of the erroring item, when it is within an array - _this will be null for attributes which are not arrays_
`message.reason` | <div class="stamp">always</div> | A string indicating the validation error

The validation error structure is designed to make it simple to integrate such errors into an end-user experience.
