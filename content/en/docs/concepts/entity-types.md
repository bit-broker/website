---
title: "Entity Types"
linkTitle: "Entity Types"
weight: 2
description: The types of entities which are the basis of a BitBroker instance
---

All information within BitBroker is stored and presented within the context of a high level enumeration called Entity Types.

Entity types are the object types which are naturally present within the domain under consideration. You can define any create and set of entity types which make sense for your deployed instance. Only [coordinator users](/docs/concepts/users/#coordinators) have the ability to [create delete and modify entity types](/docs/coordinator/entity-types/).

For example, here are some entity type enumerations which may naturally occur in different domains of operation:

Domain | Possible Entity Types
--- | ---
Transport | `bus-stop`, `bus`, `timetable`, `station`, `road`, `route`, `train`, etc
Health | `patient`, `prescription`, `doctor`, `treatment`, `condition`, etc
Manufacturing | `robot`, `tool`, `belt`, `factory`, `shift`, `quota`, `order`, etc

You should choose your entity types with care, since they are difficult to modify once system is operational.

{{% alert color="info" %}}
Entity types should be real-world concepts which an [average user](/docs/concepts/users/#consumers) would instinctively understand. They can represent physical, logical or virtual concepts within your domain. Typically, an entity type will be a _noun_ within the domain space.
{{% /alert %}}

The list of known entity types forms the entire basis of a BitBroker instance. The [Coordinator API](/docs/coordinator/) documentation contains more detail about how to go about [naming entity types](/docs/coordinator/entity-types/) and the attributes which must be present in order to [create them](/docs/coordinator/entity-types/#creating-a-new-entity-type).

#### Entity Instances

The whole point of entity types, is to provide some structure for the list of entity instances - which form the bedrock of the data which BitBroker is managing. Entity instances are submitted into the BitBroker [catalogue](/docs/concepts/catalog/) via contributions from [data connectors](/docs/concepts/connectors/).

#### Entity Schemas

BitBroker is a contribution based system, meaning that data contributed by a community of [users](/concepts/users/#contributors). In some cases, these contributors will be people you have direct control over (and may well be other roles you yourself are playing).

However, in other instances, you maybe relying upon second and third parties to be contributing data into your BitBroker instance. It is entirely possible to have multiple contributors, submitting entity instances for a shared entity type.

In scenarios where the contributor community is diverse, it can be helpful to define clear rules as to the nature and quality of the incoming data. Rules can be defined to ensure consistency of data types, formats and representation schemes. It is also vital to ensure semantic alignment between similar concepts being contributed by different users.

This can be achieved within a BitBroker system by specifying a [JSON schema](https://json-schema.org/) per entity type. Once this schema is in place, BitBroker will automatically validate all incoming records against it. Violations will be rejected and contributors [will be informed](/docs/api-principles/errors/#validation-error-format) as to the specific reasons why.

Such schemas are an optional extra and may not be required in all instances. You can specify such schemas at the points you [create](/docs/coordinator/entity-types/#creating-a-new-entity-type) and/or [modify](/docs/coordinator/entity-types/#updating-an-entity-type) entity types.
