
---
title: "Installing BitBroker"
linkTitle: "Installations"
weight: 2
description: Installing BitBroker in Kubernetes, Docker or for local development
---

There are several ways in which you can install BitBroker, depending on what you are trying to achieve.

Are you trying to deploy to a public cloud, to some local environment, to your physical machine?

Are you wanting to use BitBroker in a production environment, or as a local sandbox, perhaps you are developing [data connectors](/docs/concepts/connectors/) or even trying to [enhance BitBroker](/docs/#contributing-to-bitbroker) itself?

In this section, we will cover in detail and step-by-step, all the different ways in which you can install a BitBroker instance to help you achieve your goals.

### Full Kubernetes Installation

In the section we will explore how to you can use our pre-prepared Helm charts to install a complete BitBroker instance into your cloud of choice.

{{% alert color="primary" %}}
This section assumes you are familiar with [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="info" %}}
This flavour of deployment will give you full access to the complete feature set, including full key and policy enforcement. This will a production level system, available to all the users who have access to your cloud environment.
{{% /alert %}}

{{% alert color="info" %}}
This section assumes have already installed [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/) command line tools.
{{% /alert %}}

_Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum._

### Kubernetes Local Installation

In the section we will explore how to you can use our pre-prepared Helm charts to install a complete BitBroker instance on your local machine.

{{% alert color="primary" %}}
This section assumes you are familiar with [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="warning" %}}
This flavour of deployment will give you full access to the complete feature set, including full key and policy enforcement. But the system will only be available on your local machine.
{{% /alert %}}

{{% alert color="info" %}}
This section assumes have already installed [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/) command line tools. It also assumes you have installed and are running [Docker Desktop](https://www.docker.com/products/docker-desktop/).
{{% /alert %}}

#### Prerequisites

Start with a clean machine, with no remnants of previous BitBroker installations. Please ensure you have the following software installed, configured and operational:

* [Kubernetes](https://kubernetes.io/) command line tools
* [Helm](https://helm.sh/) command line tools
* [Docker Desktop](https://www.docker.com/products/docker-desktop/)

Make sure that your current Kubernetes context is `docker-desktop`:

```
kubectl config get-contexts
kubectl config use-context docker-desktop
```

Create a brand new directory to act as a workspace for you installation:

```
mkdir bbk
cd bbk
```

#### Installation










### Docker Compose Local Installation

In the section we will explore how to you can use our pre-prepared Docker Compose files to install a BitBroker instance on your local machine.

{{% alert color="warning" %}}
This flavour of install is suitable for _development purposes_ only.
{{% /alert %}}

{{% alert color="primary" %}}
This section assumes you are familiar with [Containers](https://www.docker.com/resources/what-container/) and [Docker](https://www.docker.com/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="warning" %}}
This flavour of deployment will give you access to a _partial_ feature set. Whilst the policy [data segment](/docs/concepts/policy/#data-segment) will be enforced, [access controls](/docs/concepts/policy/#access-control) and [key management](/docs/api-principles/authorisation/) will be bypassed. You will need to user [alternative development headers](todo) to access the [Consumer API](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
This section assumes have already installed [Docker](https://www.docker.com/) command line tools.
{{% /alert %}}

_Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum._

### Direct Installation on a Physical Machine

In the section we will explore how to you can install a BitBroker instance directly onto your local machine.

{{% alert color="warning" %}}
This flavour of install is suitable for _development purposes_ only.
{{% /alert %}}

{{% alert color="warning" %}}
This flavour of deployment will give you access to a _partial_ feature set. Whilst the policy [data segment](/docs/concepts/policy/#data-segment) will be enforced, [access controls](/docs/concepts/policy/#access-control) and [key management](/docs/api-principles/authorisation/) will be bypassed. You will need to user [alternative development headers](todo) to access the [Consumer API](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
Since you will be running BitBroker on your "bare" mahcine, this section assumes a long list of preinstalled technologies. These are outlined in detail below.
{{% /alert %}}

_Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum._
