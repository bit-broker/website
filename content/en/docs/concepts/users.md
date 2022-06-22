---
title: "Users"
linkTitle: "Users"
weight: 2
description: Users and their roles within a BitBroker instance
---

{{% alert color="primary" %}}
Read the corresponding API documentation [here](/docs/coordinator/user/)
{{% /alert %}}

All activity within BitBroker is parceled out to known Users and what they can and can't do is governed by what roles they play. In this section, we will learn about users: how to create, update and delete them and how user roles are employed to partition functionality and responsibility.

## User Roles

All user activity within BitBroker is divided between three logical user roles. Each role has its own responsibilities and access control rules. These three user types come together to form a complete system.

These are "logical" roles and it is perfectly possible for one _actual_ person to be adopting more than one role within a deployed instance.

In practice, users assume a role by being in receipt of an [access key](/docs/api-conventions/authorization/), which grants them the ability to perform actions using the corresponding API.

In the section, we will outline what the three user roles are. Later sections of this documentation will go on to  explain how to [create users and grant them such roles](/docs/coordinator/user/).

#### Coordinators

Coordinators have the most wide-ranging rights within a BitBroker system. They alone have the ability to perform key tasks such as: defining the entity types which are present, giving permission to contribute data and creating policy which governs data access. This is achieved by access to the [Coordinator API](/docs/coordinator/).

It is envisaged that there will only be a small number of coordinator users within any deployed BitBroker instance. Coordinator roles should be limited to people to whom you want to grant wide responsibility over the entire system. Whilst there can be multiple Coordinators, there can never be zero Coordinators (the system will prevent you from deleting the last Coordinator).

#### Contributors

Contributors are users who have permission to contribute data within the context of a single specified entity type, via access to the [Contributor API](/docs/contributor/). Such rights can only be granted by a coordinator user.

If a person or organization is contributing data to _many_ entity types within a single instance, they will be adopting multiple, different contributor roles in order to achieve this. Hence, the number of contributors is linked to the number of [data connectors](/docs/concepts/connectors/) which is linked to the number of [entity types](/docs/concepts/entity-types/).

#### Consumers

Consumers are the end-users of the system and the people who will be subject to the [data access policies](/docs/concepts/policy/) created by coordinators.

Consumers come in via the "front door", which is the [Consumer API](/docs/api/). They are the people who will be accessing the data to create their own applications and insights - but only ever in the context of a [policy declaration](/docs/concepts/policy/) which defines what data they can see and how they can access and use it.

There can be an unlimited number of consumer users in an active BitBroker instance.
