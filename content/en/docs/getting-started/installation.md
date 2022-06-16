
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
This flavour of deployment will give you full access to the complete BitBroker feature set, including full key and policy enforcement. This will a production level system, available to all the users who have access to your cloud environment.
{{% /alert %}}

{{% alert color="info" %}}
This section assumes have already installed [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/) command line tools.
{{% /alert %}}

_Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum._

### Kubernetes Local Installation

In the section we will explore how to you can use our pre-prepared Helm charts to install a complete BitBroker instance on your local machine. These charts will be downloaded directly from [Docker Hub](https://hub.docker.com/search?q=bbkr).

{{% alert color="primary" %}}
This section assumes you are familiar with [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="warning" %}}
This flavour of deployment will give you full access to the complete BitBroker feature set, including full key and policy enforcement. But the system will only be available on your local machine.
{{% /alert %}}

{{% alert color="info" %}}
This section assumes have already installed [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/) command line tools. It also assumes you have installed and are running [Docker Desktop](https://www.docker.com/products/docker-desktop/).
{{% /alert %}}

#### Prerequisites

Start with a clean machine, with no remnants of previous BitBroker installations. Please ensure you have the following software installed, configured and operational:

* [Kubernetes](https://kubernetes.io/) command line tools
* [Helm](https://helm.sh/) command line tools
* [Docker Desktop](https://www.docker.com/products/docker-desktop/)

{{% alert color="warning" %}}
Before starting, make sure that Kubernetes is enabled within Docker Desktop. Settings » Kubernetes » Enable Kubernetes.
{{% /alert %}}

{{% alert color="warning" %}}
Before starting, make sure that you don't have any other service listening on `port 80` of your `localhost`.
{{% /alert %}}

Create a brand new directory to act as a workspace for you installation:

```shell
mkdir bbk
cd bbk
```

Make sure that your current Kubernetes context is `docker-desktop`:

```shell
kubectl config get-contexts
kubectl config use-context docker-desktop
```

#### Installation

First let's prepare the charts we want install:

```shell
JWKS=$(docker run bbkr/auth-service:latest npm run --silent create-jwks)
kubectl apply -f https://app.getambassador.io/yaml/emissary/2.2.2/emissary-crds.yaml
helm repo add bit-broker https://bit-broker.github.io/charts
```

Our pre-prepared Helm charts are, by default, configured for production, cloud environments. However, here we want to install to `localhost` only. So we need to make some modifications to the default chart to cater for this:

```shell
helm show values bit-broker/bit-broker > values_local.yaml
sed -e 's/gateway[ ]*:/gateway: {}/g' \
    -e 's/acmeProvider/# acmeProvider/g' \
    -e 's/certificateIssuer/# certificateIssuer/g' \
    -e 's/#[ ]*host:[ ]*"https:\/\/bit-broker.io"/host: "http:\/\/localhost"/g' \
    -i '_old' \
    values_local.yaml
```

Now we are ready to run the local installation:

```shell
helm install --values values_local.yaml \
             --create-namespace bit-broker \
             --set bbk-auth-service.JWKS=$JWKS \
             --namespace bit-broker \
             bit-broker/bit-broker
```

This step takes a few moments to complete. After it has finished, you will see a series of notes which discuss some key points about your installation. The sections on JWKS, Auth Service and Rate Service are for advanced users, and you can ignore these for now.

##### Bootstrap Coordinator Token

A key thing to note is the results output, is the section which says: "Here is how to get the Coordinator token". Extracting and recording this token is a vital step to proceed with the install.

All interactions with BitBroker stem, ultimately, from interactions with the [Coordinator API](/docs/coordinator/). This is the main administrative API for the whole system. In order to use this API you need an [access token](/docs/api-principles/authorisation/).

Using this API, new users can be [created](/docs/coordinator/user/#creating-a-new-user) and then [promoted](/docs/coordinator/user/#promoting-a-user-to-coordinator)  to have coordinator status. This results in the production of a new coordinator access token for them. But this act of promotion itself, requires permission. So how can be get started with this circular scenario?

Whenever a fresh system is installed, a special _bootstrap coordinator token_ is produced. This token is valid for use with the [Coordinator API](/docs/coordinator/). You can use this token to get going with the process of then creating your own users and giving them coordinator status.

{{% alert color="warning" %}}
It is possible, but _not recommended_, to use the boostrap user in normal operation. Instead, you should use the bootstrap coordinator token to create your own master coordinator user and then utilisie their token for further operations.
{{% /alert %}}

{{% alert color="info" %}}
The bootstrap coordinator token is a different (longer) format, to normal coordinator tokens.
{{% /alert %}}

As the results output states, you can get hold of this bootstrap coordinator token as follows:

```shell
kubectl exec $(kubectl get pods --no-headers -o custom-columns=":metadata.name" --selector=app=bit-broker-bbk-auth-service -n bit-broker | head -1) -n bit-broker -c auth-service -- npm run sign-token coordinator $(kubectl get secret --namespace bit-broker bit-broker-bbk-admin-jti -o jsonpath="{.data.ADMIN_JTI}" | base64 --decode)
```

This is a long command and it will take a few seconds to complete. It will output the token, which will be in the format of a long string. Copy this token and store in a secure location. Be careful if sharing this token, as it has full rights to the entire [Coordinator API](/docs/coordinator/).

#### Testing Your Installation

If everything worked as expected, the BitBroker API servers will be up-and-running on `localhost` waiting for calls. You can test this by using the sample call below:

{{% alert color="primary" %}}
Paste in your bootstrap coordinator token into the box below, in order to update the sample call:<br/><br/>_Your Bootstrap Coordinator Token_<br/><input id="access-token" type="text" size="64" placeholder="paste token here">
{{% /alert %}}

```shell
curl http://localhost/coordinator/v1 \
     --header "x-bbk-auth-token: your-token-goes-here"
```

The base routes of all the three API servers respond with a small announcement:

```js
{
    "now": "2022-06-16T10:44:53.970Z",
    "name": "bit-broker coordinator service",
    "base": "http://localhost/coordinator/v1",
    "status": "production"
}
```

Like all BitBroker API end-points, these requires a working [authorisation](/docs/api-principles/authorisation/) to be in place. Hence, they can be used for testing or verification purposes.

#### Uninstallation

If you want to completely uninstall this instance of BitBroker, you can follow these steps:

```
helm uninstall bit-broker -n bit-broker
kubectl delete -f https://app.getambassador.io/yaml/emissary/2.2.2/emissary-crds.yaml
kubectl delete namespace bit-broker
helm repo remove bit-broker
rm values_local.*
```

This will remove all the key elements which were created as part of the installation. If you want to go further and clear out all the [images](https://kubernetes.io/docs/concepts/containers/images/) which were downloaded too, use the following:

```
todo
```

### Docker Compose Local Installation

In the section we will explore how to you can use our pre-prepared Docker Compose files to install a BitBroker instance on your local machine.

{{% alert color="warning" %}}
This flavour of install is suitable for _development purposes_ only.
{{% /alert %}}

{{% alert color="primary" %}}
This section assumes you are familiar with [Containers](https://www.docker.com/resources/what-container/) and [Docker](https://www.docker.com/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="warning" %}}
This flavour of deployment will give you access to a _partial_ BitBroker feature set. Whilst the policy [data segment](/docs/concepts/policy/#data-segment) will be enforced, [access controls](/docs/concepts/policy/#access-control) and [key management](/docs/api-principles/authorisation/) will be bypassed. You will need to user [alternative development headers](todo) to access the [Consumer API](/docs/consumer/).
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
This flavour of deployment will give you access to a _partial_ BitBroker feature set. Whilst the policy [data segment](/docs/concepts/policy/#data-segment) will be enforced, [access controls](/docs/concepts/policy/#access-control) and [key management](/docs/api-principles/authorisation/) will be bypassed. You will need to user [alternative development headers](todo) to access the [Consumer API](/docs/consumer/).
{{% /alert %}}

{{% alert color="info" %}}
Since you will be running BitBroker on your "bare" mahcine, this section assumes a long list of preinstalled technologies. These are outlined in detail below.
{{% /alert %}}

_Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum._
