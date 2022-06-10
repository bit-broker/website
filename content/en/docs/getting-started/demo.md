
---
title: "Demos and Examples"
linkTitle: "Demos & Examples"
weight: 1
description: Explore the demonstration instance of BitBroker
---

Here you will find information about a range of BitBroker demo applications and connectors to help you understand what BitBroker is and how it can operate in complex data sharing scenarios.

Most importantly it will help you get started building your own applications which use BitBroker data or your own [data connectors](/docs/concepts/connectors/) to contribute data into the system.

{{% alert color="info" %}}
Our [examples repository](https://github.com/bit-broker/examples) includes support for deploying the example connectors and applications against a BitBroker deployment, via a set of [supporting scripts](https://github.com/bit-broker/examples/tree/main/development/scripts). This can be via either [Helm](https://helm.sh/) or [Docker-Compose](https://docs.docker.com/compose/).
{{% /alert %}}

{{% alert color="info" %}}
The example connectors are deployed supporting two example datasets: [countries](https://github.com/bit-broker/bit-broker/blob/main/tests/data/country.json) and [heritage sites](https://github.com/bit-broker/bit-broker/blob/main/tests/data/heritage-site.json).
{{% /alert %}}

### Demo Applications

We have a number of example applications, which allow users to explore [policy](/docs/concepts/policy/) based access to data via the [Consumer API](/docs/consumer/).

#### Data Explorer

[This application](https://demo.bit-broker.io/apps/explorer/) allows you explore the entire [Consumer API](/docs/consumer/) by directly trying out a number of interactive scenarios. It has a set of example data and polices already pre-installed and running.

You can explore using the [Catalog API](/docs/consumer/catalog/) to try different, complex catalog queries. You can see how the results of these queries differ in the light of different policies - which you can switch between simply in the application.

Once you have executed a query and obtain [entity instance records](/docs/concepts/entity-types/#entity-instances) you can use the [Entity API](/docs/consumer/entity/) to browse the whole list and inspect the details of individual entities.

Finally, for `country` data, you can also see the [Timeseries API](/docs/consumer/timeseries/) in actions and integrated with a [charting library](https://www.chartjs.org/).

{{% alert color="info" %}}
All the [source code for this demo](https://github.com/bit-broker/examples/tree/main/apps/explorer) is available in the [examples repository](https://github.com/bit-broker/examples) of our [GitHub](https://github.com/bit-broker).
{{% /alert %}}

#### Mapping Sample

[This application](https://demo.bit-broker.io/apps/map/) allows you explore the [Consumer API](/docs/consumer/) via the medium of a mapping application. It has a set of example data and polices already pre-installed and running. The geographical attributes within the example data is used to populate a map view of the data records.

You can explore how the application outputs are changed in the light of different policies - which you can switch between simply in the application.

{{% alert color="info" %}}
All the [source code for this demo](https://github.com/bit-broker/examples/tree/main/apps/map) is available in the [examples repository](https://github.com/bit-broker/examples) of our [GitHub](https://github.com/bit-broker).
{{% /alert %}}

### Connectors

Here we provide a range of [data connectors](/docs/concepts/connectors/) to help you understand what they are and how to build your own. Indeed, it is hoped that you can simple modify one of these data connectors to achieve your own data submission aims.

{{% alert color="info" %}}
We will endeavour to increase the example data connectors here over time. Offering more choices of both data sources and implementation languages. We welcome help from the community on this and [you are encouraged](https://github.com/bit-broker/.github/blob/main/profile/README.md) to submit your own data connectors in the sample set.
{{% /alert %}}

We currently have two types of example connector:

* File based - dataset loaded direcly from a file
* RDBMS - data drawn from a relational database

All implementations upload data to the BitBroker [catalog](/docs/concepts/catalog/). They also fetch and return third-party data in their entity [webhooks](/docs/contributor/webhooks/) for the example `country` dataset, and both support time series data for the `country` dataset.

{{% alert color="info" %}}
All the [source code for these connectors](https://github.com/bit-broker/examples/tree/main/connectors) is available in the [examples repository](https://github.com/bit-broker/examples) of our [GitHub](https://github.com/bit-broker).
{{% /alert %}}

#### NodeJS » File Based

This [example connector](https://github.com/bit-broker/examples/tree/main/connectors/nodejs/file) is implemented in [NodeJS](https://nodejs.org/en/) and uses a simple file as its source data store. Key characteristics of this connector are:

* Initial dataset loaded from a file over HTTP
* Supports for a range of file types: [JSON](https://www.json.org/json-en.html), [Microsoft Excel](https://www.microsoft.com/en-gb/microsoft-365/excel), [CSV](https://en.wikipedia.org/wiki/Comma-separated_values), etc
* Implements [catalog](http://localhost:1313/docs/concepts/catalog/) updating via [sessions](http://localhost:1313/docs/contributor/records/#sessions)
* Implements all [webhook](http://localhost:1313/docs/contributor/webhooks/) end-points

#### NodeJS » RDBMS

This [example connector](https://github.com/bit-broker/examples/tree/main/connectors/nodejs/rdbms) is implemented in [NodeJS](https://nodejs.org/en/) and uses a [PostgreSQL](https://www.postgresql.org/) database as its source data store. Key characteristics of this connector are:

* Initial dataset loaded via [SQL](https://en.wikipedia.org/wiki/SQL) calls on a [relational database](https://en.wikipedia.org/wiki/Relational_database#RDBMS)
* Implements [catalog](http://localhost:1313/docs/concepts/catalog/) updating via [sessions](http://localhost:1313/docs/contributor/records/#sessions)
* Implements all [webhook](http://localhost:1313/docs/contributor/webhooks/) end-points
