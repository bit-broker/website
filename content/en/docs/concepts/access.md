---
title: "User Data Access"
linkTitle: "Access"
weight: 7
description: Managing data consumers and their associated data access tokens
---

{{% alert color="primary" %}}
Read the corresponding API documentation [here](/docs/coordinator/access/)
{{% /alert %}}

Access to data within a BitBroker instance is always permitted within the context of a [consumer](/docs/concepts/users/#consumers) and a [policy](/docs/concepts/policy/). The connection between these two system concepts is called an access.

In practice, these are manifested as authorization tokens which [authorize](/docs/api-conventions/authorization/) the consumer's calls to the [Consumer API](/docs/consumer/). Such accesses can only be [created, managed and rescinded](/docs/coordinator/access/) by [coordinator](/docs/concepts/users/#coordinators) users.

Since an access is in the context of a policy, it restricts its holder to the designated [data segment](/docs/concepts/policy/#data-segment) and [access control](/docs/concepts/policy/#access-control).
