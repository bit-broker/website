
---
title: "Configuring BitBroker"
linkTitle: "Configuration"
weight: 4
description: Configuration options for a BitBroker instance
---

In the section, we cover how you can configure your BitBroker instance to align to your specific needs.

{{% alert color="warning" %}}
Whilst it is sometimes necessary to change some configuration, you should perform such actions with care. Incorrect configuration may mean that your instance fails to start or operates incorrectly.
{{% /alert %}}

{{% alert color="info" %}}
If you follow the installation guides for either [Kubernetes](/docs/getting-started/install-k8s/) or for [local](/docs/getting-started/install-local/), then these configuration issues will be taken care of for you, in that process. There is no need to hand-configure, unless your needs are specific and unusual. 
{{% /alert %}}

### Environment Settings

In this section we outline the details of our installation environment file. This file is called `.env` and resides at the root of the BitBroker file layout.

There is _no_ master record for this in the repository, however there is a file called `.env.example`, which contains all the most common parameters. You can activate this common set by simply copying this file:

```shell
cd bit-broker
cp .env.example .env
```

Here are all the settings in `.env` which can be modified:

Parameter | Default | Description
--- | --- | ---
`APP_MODE` | `standard` | This is reserved for a future feature
`APP_SERVER_METRICS` | `false` | Enables `express-prom-bundle` to each API server
`APP_SERVER_LOGGING` | `false` | Enables `stdout` logging to each API server
`APP_FILE_LOGGING` | `false` | Enables file based logging to each API server
`APP_DATABASE` | see&nbsp;`.env.example` | PostgreSQL connection string - use `CREDENTIALS` as per the default
`APP_SECRET` | see&nbsp;`.env.example` | Instance level secret used to create secure hashes
`BOOTSTRAP_USER_EMAIL` | `noreply@bit-broker.io` | The email for the bootstrap user
`BOOTSTRAP_USER_NAME` | `Admin` | The name of the bootstrap user
`BOOTSTRAP_USER_KEY_ID` | see&nbsp;`.env.example` | This parameter reserved
`COORDINATOR_PORT` | `8001` | The listening port for the coordinator API
`COORDINATOR_BASE` | `v1` | The version of the coordinator API
`COORDINATOR_USER` | see&nbsp;`.env.example` | Database access for the coordinator API
`CONTRIBUTOR_PORT` | `8002` | The listening port for the contributor API
`CONTRIBUTOR_BASE` | `v1` | The version of the contributor API
`CONTRIBUTOR_USER` | see&nbsp;`.env.example` | Database access for the contributor API
`CONSUMER_PORT` | `8003` | The listening port for the consumer API
`CONSUMER_BASE` | `v1` | The version of the consumer API
`CONSUMER_USER` | see&nbsp;`.env.example` | Database access for the consumer API
`POLICY_CACHE` | see&nbsp;`.env.example` | Redis connection string for the policy cache
`AUTH_SERVICE` | see&nbsp;`.env.example` | End-point for the auth service
`RATE_SERVICE` | see&nbsp;`.env.example` | End-point for the rate service

You will also see some parameters starting with `TESTS_` in the `.env.example`. These are reserved parameters used by the Mocha test suite. You can ignore these values unless you are developing the core system itself.

### Advanced Use Cases

In this section we outline the details of some more advanced use cases.

{{% alert color="warning" %}}
Sorry, this section is pending. Come back soon...
{{% /alert %}}
