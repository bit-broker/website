
---
title: "Installing Locally"
linkTitle: "Install Local"
weight: 3
description: Installing BitBroker for local development
---

There are several ways in which you can install BitBroker, depending on what you are trying to achieve.

Local installations using [Docker Compose](#docker-compose-local-installation) or even direct on to your physical machine, can be a useful option for some use cases. For example, where you are developing [data connectors](/docs/concepts/connectors/) or even trying to [enhance BitBroker](/docs/#contributing-to-bitbroker) itself.

In this section, we will cover in detail and step-by-step, all the different ways in which you can install a BitBroker instance locally, to help you achieve your goals.

### Server Naming and Ports

For consistency across the system, in local and development mode we use a set of standard _logical server name and port_ for addressing the three principle API services.

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

Each API service listens on a distinct, non-clashing port. Unless you configure it otherwise, even the [docker images](#docker-compose-local-installation) are designed to start each service on its designated port.

This port mapping makes it simple and unambiguous to bring up multiple (or indeed all) of these API services on the same physical machine, without them interfering with each other. The assigned service ports are as follows:

API Service | Server Port
--- | ---
Coordinator API | `8001`
Contributor API | `8002`
Consumer API | `8003`

### Development Only Headers

When installing BitBroker locally, [authorisation](/docs/api-conventions/authorisation/) is bypassed. In this scenario, you do _not_ need to supply authorisation tokens to any API.

However, when using the [Consumer API](/docs/consumer/), you do still need to specify the policy you are using. This is so that BitBroker is aware of the [data segment](/docs/concepts/policy/#data-segment) which is in-play for consumer calls. This is achieved by specifying a development header value.

Rather than the authorisation header:

```
x-bbk-auth-token: your-token-will-go-here
```

You _instead_ use the development header, as follows:

```
x-bbk-audience: your-policy-id-will-go-here
```

Failure to specify the development header on [Consumer API](/docs/consumer/) calls when in local mode, will lead to those requests being rejected.

### Bootstrap User

Every fresh install of BitBroker comes with one preinstalled user (`uid: 1`). This user is automatically created when the system is bought-up for the first time.

As we will explain below, this user is linked to the [authorisation](/docs/api-conventions/authorisation/) of API calls. The user is also important for the up-coming [web portal](/docs/concepts/portal/) BitBroker control interface.

{{% alert color="warning" %}}
It is possible, but _not recommended_, to use the bootstrap user in normal operation. Instead, you should create your own master coordinator user and then use that for further operations.
{{% /alert %}}

### Docker Compose Local Installation

In the section we will explore how to you can use our pre-prepared Docker Compose files to install a BitBroker instance on your local machine.

{{% alert color="warning" %}}
This type of install is suitable for _development purposes_ only.
{{% /alert %}}

{{% alert color="primary" %}}
This section assumes you are familiar with [Containers](https://www.docker.com/resources/what-container/) and [Docker](https://www.docker.com/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="warning" %}}
This type of deployment will give you access to a _partial_ BitBroker feature set. Whilst the policy [data segment](/docs/concepts/policy/#data-segment) will be enforced, [access controls](/docs/concepts/policy/#access-control) and [key management](/docs/api-conventions/authorisation/) will be bypassed. You will need to user [alternative development headers](#development-only-headers) to access the [Consumer API](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
This section assumes have already installed [Docker](https://www.docker.com/) command line tools.
{{% /alert %}}

#### Prerequisites

Start with a clean machine, with no remnants of previous BitBroker installations. Please ensure you have the following software installed, configured and operational:

* [Docker](https://www.docker.com/products/personal/) command line tools
* [cURL](https://curl.se/) command line tool (used for testing installs only)

{{% alert color="warning" %}}
Before starting, make sure that you don't have any other service listening on ports which BitBroker will use for local installs.
{{% /alert %}}

Create a brand new directory to act as a workspace for you installation:

```shell
mkdir bbk
cd bbk
```

#### Installation

Let's start by cloning the main BitBroker engine from its GitHub repository:

```shell
git clone https://github.com/bit-broker/bit-broker.git
```

This will have created a `bit-broker` directory and you should move into it:

```shell
cd bit-broker
```

First let's start by preparing our instance's environment file. For a standard local install, we can simple copy the existing one which came with the repository:

```shell
cp .env.example .env
```

Now we can use our docker compose scripts to install and launch our local instance:

```shell
docker-compose -f ./development/docker-compose/docker-compose.yml up
```

At this point, your BitBroker installation is up and running on your local machine. You can test it by running the steps below.

If you are going to develop on BitBroker, either for building [data connectors](/docs/concepts/connectors/) or for [contributing to the core system](/docs/#contributing-to-bitbroker), then this mode of install can provide a fast and low friction installation technique.

#### Testing Your Installation

If everything worked as expected, the BitBroker API servers will be up-and-running on `localhost` waiting for calls. You can test this by using the sample call below. In this local mode you don't need to specify any authorisation tokens.

```shell
curl http://bbk-coordinator:8001/v1
```

If you have not applied the standard [server name and port](#server-naming-and-ports) format, then you should use `http://localhost:8001` here as your API host base URL. The base end-points of all the three API servers respond with a small announcement:

```js
{
    "now": "2022-06-16T10:44:53.970Z",
    "name": "bit-broker coordinator service",
    "base": "http://localhost/coordinator/v1",
    "status": "production"
}
```

#### Uninstallation

If you want to completely uninstall this instance of BitBroker, you can follow these steps, from the top-level `bit-broker` folder:

```shell
docker-compose -f ./development/docker-compose/docker-compose.yml down
cd ..
rm -rm bit-broker
```

This will delete the Git cloned folder created earlier.

### Direct Installation on a Physical Machine

In the section we will explore how to you can install a BitBroker instance directly onto your local machine.

{{% alert color="warning" %}}
This type of install is suitable for _development purposes_ only.
{{% /alert %}}

{{% alert color="warning" %}}
This type of deployment will give you access to a _partial_ BitBroker feature set. Whilst the policy [data segment](/docs/concepts/policy/#data-segment) will be enforced, [access controls](/docs/concepts/policy/#access-control) and [key management](/docs/api-conventions/authorisation/) will be bypassed. You will need to user [alternative development headers](#development-only-headers) to access the [Consumer API](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
Since you will be running BitBroker on your "bare" machine, this section assumes a list of preinstalled technologies, operating within the host OS. These are outlined in detail below.
{{% /alert %}}

#### Prerequisites

Start with a clean machine, with no remnants of previous BitBroker installations. Please ensure you have the following software installed, configured and operational:

* [NodeJS](https://nodejs.org/en/)
* [PostgreSQL](https://www.postgresql.org/)
* [Redis](https://redis.io/)
* [cURL](https://curl.se/) command line tool (used for testing installs only)

{{% alert color="warning" %}}
Before starting, make sure that you don't have any other service listening on ports which BitBroker will use for local installs.
{{% /alert %}}

Create a brand new directory to act as a workspace for you installation:

```shell
mkdir bbk
cd bbk
```

#### Installation

Let's start by cloning the main BitBroker engine from its GitHub repository:

```shell
git clone https://github.com/bit-broker/bit-broker.git
```

This will have created a `bit-broker` directory and you should move into it:

```shell
cd bit-broker
```

First let's start by preparing our instance's environment file. For a standard local install, we can simple copy the existing one which came with the repository:

```
cp .env.example ,env
```

For development purposes, BitBroker has a handy shell script called `bbk.sh` which can be used to manage the system in local install mode. You can use this to prepare your new clone:

```shell
./development/scripts/bbk.sh unpack
```

The `unpack` step, makes sure that all the dependant node packages needed to operate the system are downloaded and ready.

Now you can start BitBroker by simply using:

```shell
./development/scripts/bbk.sh reset
```

The `reset` command given the following output:

```shell
  » no services running
  » wiping the database...
  » starting services...

  ┌────── services running ──────┐
  │                              │
  │   90808 » bbk-consumer       │
  │   90812 » bbk-rate-limit     │
  │   90813 » bbk-auth-service   │
  │   90816 » bbk-contributor    │
  │   90817 » bbk-coordinator    │
  │                              │
  └──────────────────────────────┘
```

At this point, your BitBroker installation is up and running on your local machine. You can test it by running the steps below. But first, lets just see what other commands the `bbk.sh` script supports:

```shell
./development/scripts/bbk.sh <command>

command:

  unpack → prepares a fresh git clone
  start  → starts bbk services
  stop   → stops bbk services
  status → show bbk service status
  logs   → tails all bbk services logs
  db     → start a sql session
  wipe   → resets the bbk database
  bounce → stop » start
  reset  → stop » wipe » start
```

If you are going to develop on BitBroker, either for building [data connectors](/docs/concepts/connectors/) or for [contributing to the core system](/docs/#contributing-to-bitbroker), then this mode of install can provide a fast and low friction installation technique.

#### Testing Your Installation

If everything worked as expected, the BitBroker API servers will be up-and-running on `localhost` waiting for calls. You can test this by using the sample call below. In this local mode you don't need to specify any authorisation tokens.

```shell
curl http://bbk-coordinator:8001/v1
```

If you have not applied the standard [server name and port](#server-naming-and-ports) format, then you should use `http://localhost:8001` here as your API host base URL. The base end-points of all the three API servers respond with a small announcement:

```js
{
    "now": "2022-06-16T10:44:53.970Z",
    "name": "bit-broker coordinator service",
    "base": "http://localhost/coordinator/v1",
    "status": "production"
}
```

#### Uninstallation

If you want to completely uninstall this instance of BitBroker, you can follow these steps, from the top-level `bit-broker` folder:

```shell
./development/scripts/bbk.sh stop
cd ..
rm -rm bit-broker
```

This will delete the Git cloned folder created earlier.
