---
title: "Introduction"
linkTitle: "Introduction"
weight: 1
description: A step-by-step introduction to the BitBroker system
---

In this section, we will go through all the key concepts which you need to understand to get the best out of BitBroker. The system is made up of a series of interlocking concepts and its important to understand how these operate and interact.

#### Step # 1 - Create Users

All activity within BitBroker is parcelled out to known _Users_ and what they can and can’t do is governed by what roles they play. The first step is to learn how to create users and how to assign them roles, depending on what you want them to be able to do.

Read more about [Users](/docs/concepts/users/).

#### Step # 2 - Create Entity Types

All information within BitBroker is stored and presented within the context of a high level enumeration called _Entity Types_. These are the object types which are naturally present within the domain under consideration. The next step is to learn how to create and manage entity types.

Read more about [Entity Types](/docs/concepts/entity-types/).

#### Step # 3 - Create Data Connectors

Next, for each entity type, you can create _Data Connectors_. Each of these has permission to contribute entity instance records for their given type. BitBroker marshals and manages these contributions, through a set of rules which you can define.

Read more about [Data Connectors](/docs/concepts/connectors/).

#### Step # 4 - Populate the Catalog

Data connectors provide records to the BitBroker _Catalog_. This is a place where the existence of all the entity instances in the domain space are stored, enumerable and searchable. It also provides the route for consumers to search, discover and use entity instance data.

Read more about the [Catalog](/docs/concepts/catalog/).

#### Step # 5 - Create Policy

_Data Sharing Policies_ are the main back-bone of the BitBroker system. They are defined by coordinator users, who use them to specify the exact context in which they permit data to be accessed by consumers. In this step you can learn how to define and manage these vital policies.

Read more about [Policies](/docs/concepts/policy/).

#### Step # 6 - Grant Access

Once all other elements are in place, you can proceed to grant _Access_ to data. In BitBroker, such grants are only permitted within the context of a consumer user and a policy. The connection between these two system concepts is called an access.

Read more about granting [Access](/docs/concepts/access/).
