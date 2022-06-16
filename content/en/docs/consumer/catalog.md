---
title: "Catalog API"
linkTitle: "Catalog API"
weight: 1
description: The catalog API enabling search and discovery services for entity instances
---

All the entity instances being managed by BitBroker are recorded with [the catalog](/concepts/catalog/). This Catalog API is used to search this index and to discover entity instances which are needed by applications. The Catalog API returns list of entity instances, which are then accessible via the [Entity API](/docs/consumer/entity/).

In this section, we will explore the capabilities of the Catalog API in depth.

{{% alert color="primary" %}}
A quick way to get going with building your own applications is to adapt the [example apps](/docs/examples/applications/) which use this and the other [Consumer APIs](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
All Consumer API calls happen within the context of a [data sharing policy](/docs/concepts/policy/). The policy defines a data segment which you are allowed to access. Any queries which you make using the Catalog API can only operate within the data segment you are permitted access to.
{{% /alert %}}

{{% alert color="primary" %}}
All API calls in BitBroker require [authorisation](/docs/api-principles/authorisation/). The sample calls below contain a placeholder string where you should insert your [consumer API token](/docs/api-principles/authorisation/#obtaining-a-consumer-key). This key should have been provided to you by the coordinator user who administers the BitBroker instance. If you already have a token, enter it in the box below to update all the sample calls on this page:<br/><br/>_Your Consumer API Token_<br/><input id="access-token" type="text" size="64" placeholder="paste token here">
{{% /alert %}}

## Querying the Catalog

You can query the catalog by issuing an `HTTP/GET` to the `/catalog` end-point.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --header "x-bbk-auth-token: your-token-goes-here"
```

This will return an empty JSON array.

The catalog API will return a list of entity instances which match the submitted query string. The query string is submitted by adding a `q` URL parameter to the call. Submitting no query string, results in an empty array (as opposed to all items).

{{% alert color="info" %}}
Lists retrieved from this API will be returned in [pages](/docs/consumer/#paging-lists)
{{% /alert %}}

For example, using the query string:

```js
q={"name" : "India"}
```

Would return the following JSON array:

```js
[
    {
        "id": "917d0311c687e5ffb28c91a9ea57cd3a306890d0",
        "url": "http://bbk-consumer:8003/v1/entity/country/917d0311c687e5ffb28c91a9ea57cd3a306890d0",
        "type": "country",
        "name": "India",
        "legal": []
    }
]
```

Here we have one matching entity instance. The API returns [Entity API](/docs/consumer/entity/) links to matching instance.

{{% alert color="primary" %}}
In the examples here we show the query strings in readable JSON format, but when actual calls are made the query string is required to be [url encoded](https://www.w3schools.com/tags/ref_urlencode.asp).
{{% /alert %}}

### Querying Options

Here we will go through each available query option one-by-one, giving an example of each.

{{% alert color="info" %}}
All query strings are [url encoded](https://www.w3schools.com/tags/ref_urlencode.asp) JSON objects. The query string syntax used is loosely based on the query language used within [Mongo DB](https://www.mongodb.com/).
{{% /alert %}}

#### Implicit Equality

This query format is a shorthand for using the `$eq` operator

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "name": "India" }'
```

#### Equals / Not Equals

The `$eq` and `$ne` operators work for a range of data types such as integers, floats and strings.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "entity.capital": { "$eq": "London" } }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "entity.capital": { "$ne": "Paris" } }'
```

#### Less Than (or Equal to)

The `$lt` and `$lte` operators work for a range of data types such as integers, floats and strings.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "entity.population": { "$lt": 100000 } }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "entity.population": { "$lte": 100000 } }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "name": { "$lt": "China"} }'
```

#### Greater Than (or Equal to)

The `$gt` and `$gte` operators work for a range of data types such as integers, floats and strings.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "entity.population": { "$gt": 100000 } }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "entity.population": { "$gte": 100000 } }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "name": { "$gt": "Mexico"} }'
```

#### In / Not In

The `$in` and `$nin` operators are for searching within arrays only.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "name": {
         "$in": ["United Kingdom", "India", "France"]
     } }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "name": {
         "$nin": ["United Kingdom", "India", "France"]
     } }'
```

#### Logical Operators

The `$and`, `$or` and `$nor` operators can be combined in any combination.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "$and": [
         { "entity.continent": "Europe" },
         { "entity.population": { "$gt": 50000000 } }
     ] }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "$or": [
         { "entity.continent": "Europe" },
         { "entity.population": { "$gt": 50000000 } }
     ] }'
```

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "$nor": [
         { "name": "United Kingdom" },
         { "entity.population": { "$gt": 50000000 } }
     ] }'
```

#### Regular Expressions

The `$regex` operator allows for the use of [regular expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) to query any string attribute.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={ "name":
         { "$regex": "United .*", "$options": "i" }
     }'
```

#### Geo Spatial Queries

The `$near` operator is used to find entity instances close to a [GeoJSON](https://geojson.org/) geometry. The `$min` and `$max` parameters are specified in metres. You must specify either one of `$min` or `$max` or both together. The `$geometry` attribute must be present.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={
         "entity.location": {
             "$near": {
                 "$max": 750000,
                 "$min": 0,
                 "$geometry": {
                     "type": "Point",
                     "coordinates": [
                         -3.435973,
                         55.378051
                     ]
                 }
             }
         }
     }'
```

The `$within` operator is used to find entity instances inside a closed [GeoJSON](https://geojson.org/) geometry. The `$geometry` attribute must be present.

```shell
curl http://bbk-consumer:8003/v1/catalog \
     --get \
     --header "x-bbk-auth-token: your-token-goes-here" \
     --data-urlencode 'q={
        "entity.location": {
          "$within": {
            "$geometry": {
              "type": "Polygon",
              "coordinates": [
                [
                  [ -12.386341, 59.062341 ],
                  [ -12.386341, 49.952269 ],
                  [   2.500282, 49.952269 ],
                  [   2.500282, 59.062341 ],
                  [ -12.386341, 59.062341 ]
                ]
              ]
            }
          }
        }
      }'
```
