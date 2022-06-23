---
title: "Entity API"
linkTitle: "Entity API"
weight: 2
description: The entity API enabling direct access to entity types and entity instances
---

The Entity API is used to retrieve information about entity instances which are present within [the catalog](/docs/concepts/catalog/). You can use this API to get a list of such entity instances or to get details of one particular entity instance. Calls to the Entity API are most often a result of a query to the [Catalog API](/docs/consumer/catalog/).

In this section, we will explore the capabilities of the Entity API in depth.

{{% alert color="primary" %}}
A quick way to get going with building your own applications is to adapt the [example apps](/docs/examples/applications/) which use this and the other [Consumer APIs](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
All Consumer API calls happen within the context of a [data sharing policy](/docs/concepts/policy/). The policy defines a data segment which you are allowed to access. Any queries which you make using the Entity API can only operate within the data segment you are permitted access to.
{{% /alert %}}

{{% alert color="primary" %}}
In our sample calls, we use the standard [server name and port](/docs/getting-started/install-local/#server-naming-and-ports) format outlined for [local installations](/docs/getting-started/install-local/) and [demos](/docs/getting-started/demo/). If you have an alternative API host base URL, then enter it in the box below to update all the sample calls on this page :<br/><br/>_Your API Host Base URL_<br/><input class="code-replace" data-item="http://bbk-consumer:8003" data-name="base url" type="text" placeholder="enter url here">
{{% /alert %}}

{{% alert color="primary" %}}
The Consumer API can be run in production _or_ development mode; each requiring a different header. If you are running a [local installation](/docs/getting-started/install-local/) of BitBroker, then you can switch the sample calls on this page to development mode by activating the toggle below. If you are unsure, leave this toggle off.<br/>
<div class="toggle">
  <label class="switch">
    <input class="header" type="checkbox">  
    <span class="slider">
      <span>Apply development mode</span>
    </span>
  </label>
</div>
{{% /alert %}}

{{% alert id="mode-prd" color="primary" %}}
All API calls in BitBroker require [authorization](/docs/api-conventions/authorization/). The sample calls below contain a placeholder string where you should insert your [consumer API authorization token](/docs/api-conventions/authorization/#obtaining-a-consumer-token). This authorization token should have been provided to you by the coordinator user who administers the BitBroker instance. If you already have a token, enter it in the box below to update all the sample calls on this page:<br/><br/>_Your Consumer API Authorization Token_<br/><input class="code-replace" data-item="your-header-goes-here" data-name="header" type="text" placeholder="enter token here">
{{% /alert %}}

{{% alert id="mode-dev" color="primary" %}}
When installing BitBroker locally, [authorization](/docs/api-conventions/authorization/) is bypassed. In this scenario, you need to specify the policy you are using in a [development header](/docs/getting-started/install-local/#development-only-headers). Enter your desired policy in the box below to update all the sample calls on this page:<br/><br/>_Your Current Policy_<br/><input class="code-replace" data-item="your-header-goes-here" data-name="header" type="text" placeholder="enter policy id here" value="access-all-areas">
{{% /alert %}}

### Entity Types Lists

You can query for a list of known entity types by issuing an `HTTP/GET` to the `/entity` end-point.

```shell
curl http://bbk-consumer:8003/v1/entity \
     --header "x-bbk-auth-token: your-header-goes-here"
```

This will return a JSON array as follows:

```js
[
    {
        "id": "country",
        "url": "http://bbk-consumer:8003/v1/entity/country",
        "name": "Countries",
        "description": "All the countries in the world as defined by the UN"
    },
    {
        "id": "heritage-site",
        "url": "http://bbk-consumer:8003/v1/entity/heritage-site",
        "name": "World Heritage Sites",
        "description": "A landmark or area with legal protection by an international convention administered by UNESCO"
    }
    // other entity types here
]
```

Each entity type, which you are permitted to see, will be returned within this array.

{{% alert color="info" %}}
Lists retrieved from this API will be returned in [pages](/docs/consumer/#paging-lists)
{{% /alert %}}

### Entity Instance Lists

You can query for a list of entity instances of a given entity type by issuing an `HTTP/GET` to the `/entity/:type` end-point.

```shell
curl http://bbk-consumer:8003/v1/entity/country \
     --header "x-bbk-auth-token: your-header-goes-here"
```

This will return an empty JSON array as follows:

```js
[
    {
        "id": "d38e86a5591b1f6e562040b9189556ff2d190ea7",
        "url": "http://bbk-consumer:8003/v1/entity/country/d38e86a5591b1f6e562040b9189556ff2d190ea7",
        "type": "country",
        "name": "Andorra",
        "legal": []
    },
    {
        "id": "34c3ab32774042098ddc0ffa9878e4a1a60b33c0",
        "url": "http://bbk-consumer:8003/v1/entity/country/34c3ab32774042098ddc0ffa9878e4a1a60b33c0",
        "type": "country",
        "name": "United Arab Emirates",
        "legal": []
    },
    {
        "id": "8c52885171d12b5cda6c77e2b9e9d52ed6bfe867",
        "url": "http://bbk-consumer:8003/v1/entity/country/8c52885171d12b5cda6c77e2b9e9d52ed6bfe867",
        "type": "country",
        "name": "Afghanistan",
        "legal": []
    },
    // other entity instances here
]
```

Each entity instance, which you are permitted to see, will be returned within this array.

{{% alert color="info" %}}
Lists retrieved from this API will be returned in [pages](/docs/consumer/#paging-lists)
{{% /alert %}}

### Entity Instance Details

You can get the details of a particular entity instance by issuing an `HTTP/GET` to the `/entity/:type/:id` end-point.

```shell
curl http://bbk-consumer:8003/v1/entity/country/34c3ab32774042098ddc0ffa9878e4a1a60b33c0 \
     --header "x-bbk-auth-token: your-header-goes-here"
```

This will return a JSON object as follows:

```js
{
    "id": "34c3ab32774042098ddc0ffa9878e4a1a60b33c0",
    "url": "http://bbk-consumer:8003/v1/entity/country/34c3ab32774042098ddc0ffa9878e4a1a60b33c0",
    "type": "country",
    "name": "United Arab Emirates",
    "entity": {
        "area": 83600,
        "code": "AE",
        "link": "https://en.wikipedia.org/wiki/United_Arab_Emirates",
        "capital": "Abu Dhabi",
        "currency": {
            "code": "AED",
            "name": "Arab Emirates Dirham"
        },
        "location": {
            "type": "Point",
            "coordinates": [
                53.847818,
                23.424076
            ]
        },
        "continent": "Asia",
        "population": 9682088,
        "calling_code": 971,
    },
    "instance": {
        "independence": 1971
    },
    "timeseries": {
        "population": {
            "unit": "x1000",
            "value": "people",
            "period": "P1Y",
            "url": "http://bbk-consumer:8003/v1/entity/country/34c3ab32774042098ddc0ffa9878e4a1a60b33c0/timeseries/population"
        }
    },
    "legal": []
}
```

You can only get details of entity instances which you are permitted to see.
