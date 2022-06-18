
---
title: "Installing Using Kubernetes"
linkTitle: "Install Kubernetes"
weight: 2
description: Installing BitBroker on Kubernetes, either for cloud or for local development
---

There are several ways in which you can install BitBroker, depending on what you are trying to achieve.

Are you trying to deploy to a public cloud or to a local environment? Are you wanting to use BitBroker in a production environment, or as a local sandbox, perhaps you are developing [data connectors](/docs/concepts/connectors/) or even trying to [enhance BitBroker](/docs/#contributing-to-bitbroker) itself?

In this section, we will cover in detail and step-by-step, all the different ways in which you can install a BitBroker instance using Kubernetes, to help you achieve your goals.

### Bootstrapping Fresh Installations

For all Kubernetes installations, you should be aware of the ways in which fresh installations perform certain bootstrap operations.

#### Bootstrap User

Every fresh install of BitBroker comes with one preinstalled user (`uid: 1`). This user is automatically created when the system is bought-up for the first time.

As we will explain below, this user is linked to the [authorisation](/docs/api-principles/authorisation/) of API calls. The user is also important for the up-coming [web portal](/docs/concepts/portal/) BitBroker control interface.

#### Bootstrap Coordinator Token

All interactions with BitBroker stem, ultimately, from interactions with the [Coordinator API](/docs/coordinator/). This is the main administrative API for the whole system. In order to use this API you need an [access token](/docs/api-principles/authorisation/).

Using this API, new users can be [created](/docs/coordinator/user/#creating-a-new-user) and then [promoted](/docs/coordinator/user/#promoting-a-user-to-coordinator)  to have coordinator status. This results in the production of a new coordinator access token for them. But this act of promotion itself, requires permission. So how can be get started with this circular scenario?

Whenever a fresh system is installed using Kubernetes, a special _bootstrap coordinator token_ is produced. This token is valid for use with the [Coordinator API](/docs/coordinator/). You can use this token to get going with the process of then creating your own users and giving them coordinator status.

{{% alert color="warning" %}}
It is possible, but _not recommended_, to use the bootstrap coordinator token in normal operation. Instead, you should use it to create your own master coordinator user and then utilise their token for further operations.
{{% /alert %}}

{{% alert color="info" %}}
The bootstrap coordinator token is a different (longer) format, to normal coordinator tokens.
{{% /alert %}}

The detailed install steps below, will contain more information about how to extract and use this bootstrap token.

### Cloud Kubernetes Installation

In the section we will explore how to you can use our pre-prepared Helm charts to install a complete BitBroker instance into your cloud of choice. These charts will be downloaded directly from [Docker Hub](https://hub.docker.com/search?q=bbkr).

{{% alert color="primary" %}}
This section assumes you are familiar with [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="info" %}}
This type of deployment will give you full access to the complete BitBroker feature set, including full policy enforcement. The system will be accessible to anyone to whom you grant access.
{{% /alert %}}

#### Prerequisites

Start with a clean machine, with no remnants of previous BitBroker installations. Please ensure you have the following software installed, configured and operational:

* [Kubernetes](https://kubernetes.io/) command line tools
* [Helm](https://helm.sh/) command line tools
* [cURL](https://curl.se/) command line tool (used for testing installs only)

Create a brand new directory to act as a workspace for you installation:

```shell
mkdir bbk
cd bbk
```

Make sure that your current Kubernetes context is pointing at your cloud of choice:

```shell
kubectl config get-contexts
```

#### Installation

First let's prepare the charts we want install:

```shell
JWKS=$(docker run bbkr/auth-service:latest npm run --silent create-jwks)
kubectl apply -f https://app.getambassador.io/yaml/emissary/2.2.2/emissary-crds.yaml
helm repo add bit-broker https://bit-broker.github.io/charts
```

Now we are ready to run the cloud installation:

```shell
helm install --values values.yaml \
             --create-namespace bit-broker \
             --set bbk-auth-service.JWKS=$JWKS \
             --namespace bit-broker \
               bit-broker/bit-broker
```

This step takes a few moments to complete. After it has finished, you will see a series of notes which discuss some key points about your installation. The sections on JWKS, Auth Service and Rate Service are for [advanced use cases](/docs/getting-started/configuration/#advanced-use-cases), and you can ignore these for now.

It can take a few moments for the system to come into existence and for it to complete its initialisation steps. You can test that the system is up-and-ready, by the using this command:

```shell
if [ $(curl --max-time 5 --write-out '%{http_code}' --silent --head --output /dev/null http://your-cloud-host/coordinator/v1) == "401" ]; then echo "Ready"; else echo "Not Ready"; fi;
```

This will output `Not Ready` until all the servers are up, after which it will output `Ready`. Keep trying this command until it signals its OK to proceed.

##### Bootstrap Coordinator Token

A key thing to note in the results output, is the section which says: "Here is how to get the Coordinator token". Extracting and recording this token is a vital step to proceed with the install. This is the [bootstrap coordinator token](#bootstrap-coordinator-token), which we outlined earlier in this document.

As the results output states, you can get hold of this bootstrap coordinator token as follows:

```shell
kubectl exec $(kubectl get pods --no-headers -o custom-columns=":metadata.name" --selector=app=bit-broker-bbk-auth-service -n bit-broker | head -1) -n bit-broker -c auth-service -- npm run sign-token coordinator $(kubectl get secret --namespace bit-broker bit-broker-bbk-admin-jti -o jsonpath="{.data.ADMIN_JTI}" | base64 --decode)
```

This is a long command and it will take a few seconds to complete. It will output the token, which will be in the format of a long string. Copy this token and store in a secure location. Be careful if sharing this token, as it has full rights to the entire [Coordinator API](/docs/coordinator/).

#### Testing Your Installation

If everything worked as expected, the BitBroker API servers will be up-and-running in your cloud waiting for calls. You can test this by using the sample call below:

{{% alert color="primary" %}}
Paste in your bootstrap coordinator token into the box below, in order to update the sample call:<br/><br/>_Your Bootstrap Coordinator Token_<br/><input class="code-replace" data-item="your-token-goes-here" type="text" size="64" placeholder="paste token here">
{{% /alert %}}

```shell
curl http://your-cloud-host/coordinator/v1 \
     --header "x-bbk-auth-token: your-token-goes-here"
```

The base routes of all the three API servers respond with a small announcement:

```js
{
    "now": "2022-06-16T10:44:53.970Z",
    "name": "bit-broker coordinator service",
    "base": "http://your-cloud-host/coordinator/v1",
    "status": "production"
}
```

Like all BitBroker API end-points, these requires a working [authorisation](/docs/api-principles/authorisation/) to be in place. Hence, this announcement can be used for testing or verification purposes.

#### Uninstallation

If you want to completely uninstall this instance of BitBroker, you should follow these steps:

```shell
helm uninstall bit-broker -n bit-broker
kubectl delete -f https://app.getambassador.io/yaml/emissary/2.2.2/emissary-crds.yaml
kubectl delete namespace bit-broker
helm repo remove bit-broker
docker ps -a | grep "bbkr/" | awk '{print $1}' | xargs docker rm
rm values_local.*
```

This will remove all the key elements which were created as part of the installation. If you want to go further and clear out all the [images](https://kubernetes.io/docs/concepts/containers/images/) which were downloaded too, use the following:

```shell
docker images -a | grep "bbkr/" | awk '{print $3}' | uniq | xargs docker rmi
```

### Local Kubernetes Installation

In the section we will explore how to you can use our pre-prepared Helm charts to install a complete BitBroker instance on your local machine. These charts will be downloaded directly from [Docker Hub](https://hub.docker.com/search?q=bbkr).

{{% alert color="warning" %}}
This type of install is suitable for _development purposes_ only.
{{% /alert %}}

{{% alert color="primary" %}}
This section assumes you are familiar with [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/). If you are new to these technologies, we advise that you become familiar with them first, before trying out the steps outlined below.
{{% /alert %}}

{{% alert color="warning" %}}
This type of deployment will give you full access to the complete BitBroker feature set, including full policy enforcement. But the system will only be available on your local machine.
{{% /alert %}}

{{% alert color="info" %}}
This section assumes have already installed [Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh/) command line tools. It also assumes you have installed and are running [Docker Desktop](https://www.docker.com/products/docker-desktop/).
{{% /alert %}}

#### Prerequisites

Start with a clean machine, with no remnants of previous BitBroker installations. Please ensure you have the following software installed, configured and operational:

* [Kubernetes](https://kubernetes.io/) command line tools
* [Helm](https://helm.sh/) command line tools
* [Docker Desktop](https://www.docker.com/products/docker-desktop/)
* [cURL](https://curl.se/) command line tool (used for testing installs only)

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

This step takes a few moments to complete. After it has finished, you will see a series of notes which discuss some key points about your installation. The sections on JWKS, Auth Service and Rate Service are for [advanced use cases](/docs/getting-started/configuration/#advanced-use-cases), and you can ignore these for now.

It can take a few moments for the system to come into existence and for it to complete its initialisation steps. You can test that the system is up-and-ready, by the using this command:

```shell
if [ $(curl --max-time 5 --write-out '%{http_code}' --silent --head --output /dev/null http://localhost/coordinator/v1) == "401" ]; then echo "Ready"; else echo "Not Ready"; fi;
```

This will output `Not Ready` until all the servers are up, after which it will output `Ready`. Keep trying this command until it signals its OK to proceed.

##### Bootstrap Coordinator Token

A key thing to note in the results output, is the section which says: "Here is how to get the Coordinator token". Extracting and recording this token is a vital step to proceed with the install. This is the [bootstrap coordinator token](#bootstrap-coordinator-token), which we outlined earlier in this document.

As the results output states, you can get hold of this bootstrap coordinator token as follows:

```shell
kubectl exec $(kubectl get pods --no-headers -o custom-columns=":metadata.name" --selector=app=bit-broker-bbk-auth-service -n bit-broker | head -1) -n bit-broker -c auth-service -- npm run sign-token coordinator $(kubectl get secret --namespace bit-broker bit-broker-bbk-admin-jti -o jsonpath="{.data.ADMIN_JTI}" | base64 --decode)
```

This is a long command and it will take a few seconds to complete. It will output the token, which will be in the format of a long string. Copy this token and store in a secure location. Be careful if sharing this token, as it has full rights to the entire [Coordinator API](/docs/coordinator/).

#### Testing Your Installation

If everything worked as expected, the BitBroker API servers will be up-and-running on `localhost` waiting for calls. You can test this by using the sample call below:

{{% alert color="primary" %}}
Paste in your bootstrap coordinator token into the box below, in order to update the sample call:<br/><br/>_Your Bootstrap Coordinator Token_<br/><input class="code-replace" data-item="your-token-goes-here" type="text" size="64" placeholder="paste token here">
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

Like all BitBroker API end-points, these requires a working [authorisation](/docs/api-principles/authorisation/) to be in place. Hence, this announcement can be used for testing or verification purposes.

#### Uninstallation

If you want to completely uninstall this instance of BitBroker, you should follow these steps:

```shell
helm uninstall bit-broker -n bit-broker
kubectl delete -f https://app.getambassador.io/yaml/emissary/2.2.2/emissary-crds.yaml
kubectl delete namespace bit-broker
helm repo remove bit-broker
docker ps -a | grep "bbkr/" | awk '{print $1}' | xargs docker rm
rm values_local.*
```

This will remove all the key elements which were created as part of the installation. If you want to go further and clear out all the [images](https://kubernetes.io/docs/concepts/containers/images/) which were downloaded too, use the following:

```shell
docker images -a | grep "bbkr/" | awk '{print $3}' | uniq | xargs docker rmi
```
