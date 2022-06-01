---
title: "Catalog API"
linkTitle: "Catalog API"
weight: 1
description: The catalog API enabling search and discovery services for entity instances
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

__Implicit Equality__

```
{ 'name': 'United Kingdom' }
```

__Equals / Not Equals__
```
{ 'entity.capital': { '$eq': 'London' } }
{ 'entity.capital': { '$ne': 'Paris' } }
```

__Less Than (or Equal to)__
```
{ 'entity.population': { '$lt': 100000 } }
{ 'entity.population': { '$lte': 100000 } }
{ 'name': { '$lt': 'China'} }
```

__Greater Than (or Equal to)__
```
{ 'entity.population': { '$gt': 10000000 } }
{ 'entity.population': { '$gte': 10000000 } }
{ 'name': { '$gt': 'Mexico'} }
```

__In / Not In__
```
{ 'name': { '$in': ['United Kingdom', 'India', 'France'] } }
{ 'name': { '$nin': ['United Kingdom', 'India', 'France'] } }
```

__Logical Operators__
```
{ '$and': [
    { 'entity.continent': 'Europe' },
    { 'entity.population': { '$gt': 50000000 } }
] }

{ '$or': [
    { 'entity.literacy': { '$lt': 0.5 } },
    { 'entity.population': { '$gt': 50000000 } }
] }

{ '$nor': [
    { 'name': 'United Kingdom' },
    { 'entity.population': { '$gt': 50000000 }
] }

{ '$not': { 'name': 'United Kingdom' } }
```

__Regular Expressions__
```
{ 'name': { '$regex': 'United .*', '$options': 'i' } }
```

__Geo Spatial Queries__
```
{ 'entity.location': { '$near': { '$geometry': {
    type: 'Point',
    coordinates: [-3.435973, 55.378051] }, '$minDistance': 0, '$maxDistance': 750000 } } }

{ 'entity.location': { '$within': { '$geometry': {
    type: 'Polygon',
    coordinates: [
        [
            [-12.386341, 59.062341],
            [-12.386341, 49.952269],
            [2.500282, 49.952269],
            [2.500282, 59.062341],
            [-12.386341, 59.062341]
        ]
    ] } } }
```
