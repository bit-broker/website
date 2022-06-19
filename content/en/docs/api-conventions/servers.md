---
title: "Server Names and Ports"
linkTitle: "Server Naming"
weight: 2
description: Standard logical server names and ports
---

For consistency across the entire system, we use a set of standard _logical server names and ports_ for addressing the three principle API services.

This helps with readability and removes ambiguity, since some APIs share resource names. Also it reduces confusion, if you start multiple API servers on the same physical machine.

#### Logical Server Names

We use a convention of assigning each API service a logical server name as follows:

API Service | Logical Server Name
--- | ---
Coordinator API | `bbk-coordinator`
Contributor API | `bbk-contributor`
Consumer API | `bbk-consumer`

You will find these names used across all the [documentation](/docs) and in the [sample code](/docs/examples/). This is merely a convention and you do not need to use these names in your code. You can, instead, use your cloud URLs or even base IP addresses.

_If_ you choose to stick to this convention, you will need to map these name to their ultimate end-points inside your [system hosts file](https://en.wikipedia.org/wiki/Hosts_(file)). Here is an example, mapping the standard logical server names to `localhost`.

```
127.0.0.1 bbk-coordinator
127.0.0.1 bbk-contributor
127.0.0.1 bbk-consumer
```

#### Server Ports

Each API service listens on a distinct, non-clashing port. Unless you configure it otherwise, even the [docker images](/docs/getting-started/install-local/#docker-compose-local-installation) are designed to start each service on its designated port.

This port mapping makes it simple and unambiguous to bring up multiple (or indeed all) of these API services on the same physical machine, without them interfering with each other. The assigned service ports are as follows:

API Service | Server Port
--- | ---
Coordinator API | `8001`
Contributor API | `8002`
Consumer API | `8003`
