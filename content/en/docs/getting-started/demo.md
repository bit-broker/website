
---
title: "Demos and Examples"
linkTitle: "Demos & Examples"
weight: 1
description: Explore the demonstration instance of BitBroker 
---

Here you will find information about a range of BitBroker demo applications and connectors to help you understand what BitBroker is and how it can operate in complex data sharing scenarios.

Most importantly it will help you get started building your applications which use BitBroker data or your [data connectors](/docs/concepts/connectors/) to contribute data into the system.

### Demo Applications

We have a number of example applications, which allow users to explore [policy](/docs/concepts/policy/) based access to data via the [Consumer API](/docs/consumer/).

#### Policy-Based Data Explorer

[This application](todo) allows you explore the entire [Consumer API](/docs/consumer/) by directly trying out a number of interactive scenarios. It has a set of example data and polices already pre-installed and running.

You can explore using the [Catalog API](/docs/consumer/catalog/) to try different, complex catalog queries. You can see how the results of these queries differ in the light of different policies - which you can switch between simply in the application.

Once you have executed a query and obtain [entity instance records](/docs/concepts/entity-types/#entity-instances) you can use the [Entity API](/docs/consumer/entity/) to browse the whole list and inspect the details of individual entities.

Finally, for `country` data, you can also see the [Timeseries API](/docs/consumer/timeseries/) in actions and integrated with a [charting library](https://www.chartjs.org/).

{{% alert color="info" %}}
All the [source code for this demo](https://github.com/bit-broker/examples/tree/main/apps/explorer) is available in the [examples repo](https://github.com/bit-broker/examples) of our [GitHub](https://github.com/bit-broker).
{{% /alert %}}

## Maps

This app allows users to access countries & heritage site entity data presented in a map context, again according to the policy selected from a set of pre-defined policies.

## Connectors

We have two types of example connector:

- file-based (initial data set loaded from a file)
- RDBMS (PostGres)

Both implementations fetch and merge 3rd party data in their entity webhooks for the country data set, and both support timeseries data for the country data set.

Our initial implementations are node javascript; we aim to expand the range of language implementations in future.

### nodejs-file

Node js file-based data connector.

- initial data set loaded from a file over http
- supports json, xlsx & csv formatted data
- implements catalog session
- implements entity & timeseries webhooks

### nodejs-RDBMS

PostGreSQL backed data connector.

- initial data set loaded from PostGreSQL
- implements catalog session
- implements entity & timeseries webhooks

## Build & Deployment

This repository includes support for deploying the example connectors and apps against a BitBroker deployment, either using Helm or Docker-Compose, with a set of supporting scripts. The Connectors are deplyed supporting two data sets, countries & heritage sites.

see [README.MD](./development/scripts/README.MD)
