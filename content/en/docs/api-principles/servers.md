---
title: "Server Names and Ports"
linkTitle: "Server Naming"
weight: 2
description: Standard logical server names and ports
---

For consistency across the whole system, we use a set of standard _logical server names and ports_ for addressing the three principle API services. This helps with readability and removes ambiguity, since some APIs share resource names. Also it reduces confusion if you start multiple servers on the same physical machine.

#### Logical Server Name

We use a convention of assigning each API service a logical server name as follows:

API Service | Logical Server Name
--- | ---
Coordinator API | `bbk-coordinator`
Contributor API | `bbk-contributor`
Consumer API | `bbk-consumer`

You will find these names used across all the documentation and in the sample code. This is merely a convention and you do not need to use these names in your code. You can, instead, use your cloud URLs or even base IP addresses.

_If_ you choose to stick to this convention, you will need to map these name to their ultimate end-points inside your [system hosts file](https://en.wikipedia.org/wiki/Hosts_(file)). Here is an example, mapping the standard server names to `localhost`.

```
127.0.0.1 bbk-coordinator
127.0.0.1 bbk-contributor
127.0.0.1 bbk-consumer
```

#### Server Ports

Each API service listens on a distinct, non-clashing port. Unless you configure it otherwise, even the [docker images](todo) are designed to start-up on the services designate port.

This method makes it simple and unambiguous to bring up multiple (or all) of these services on the same physical machine, without them interfering with each other. The assigned service ports are as follows:

API Service | Server Port
--- | ---
Coordinator API | `8001`
Contributor API | `8002`
Consumer API | `8003`
