
---
title: Welcome to BitBroker
linkTitle: Documentation
cid: welcome
menu:
  main:
    weight: 20
---

This documentation explains all the concepts, models and processes that you will need to understand to get started with BitBroker.

{{% alert color="primary" %}}
This documentation is still work-in-progress and will be updated as the project progresses.
{{% /alert %}}

## What is BitBroker?

_BitBroker is an open source, end-to-end, data sharing solution._

It allows organizations to share data with third-parties in the context of robust policy definitions. These policies ensure that:

* Agreed people can get access to the data
* … only the specified data segment is visible to them
* … they access the data in the permitted manner
* … everything happens within a defined legal framework

{{% alert color="info" %}}
BitBroker lets data owners share data with confidence.
{{% /alert %}}

Using BitBroker you can leverage the expertise of external players to help analyze, process and realize the latent value within your data. Services provided by the system are:

Service | Description
 ---: | ---
 &nbsp;Connect heterogeneous data sets | BitBroker is a great way to bring order into an otherwise chaotic data environment. It helps organize data from complex and heterogeneous backend systems, presenting a more consistent view to data consumers.
Controlled data contribution | BitBroker allows for a community of people to contribute data; however, it is not a free-for-all. Coordinators decide _who_ can contribute and _what_ they can contribute. They can set schematic expectations per data type, allowing BitBroker to police all contributions to ensure they are compliant.
Comprehensive cataloging | Unless asked it to, BitBroker _does not take a copy_ of data. Instead, it indexes all the entity instances in the domain and facilitates the search and discovery of these. Policy based access is then brokered via the core engine, meaning that the consumer is never made aware of the source details of any data.
Flexible data sharing policies | Coordinators can create and deploy any number of data sharing policies. These define which data segments are visible, how they can be accessed and the legal basis for any use. Once deployed, BitBroker will police the policy on your behalf, giving you confidence that data is only being accessed and used as you would want it to be.
Reach-for data licenses | BitBroker eases the legal burden of sharing data by allowing Coordinators to select and incorporate from common data licenses, attribution models, terms and conditions, acceptable content, etc. This means they can quickly get started with data consumers, but still within a robust legal framework.
Modern, effective suite of APIs | BitBroker works out-of-the-box with a comprehensive suite of modern APIs offering search, discovery and access to the policy specified entity and time series data.
Complete user and key management | Flexible key workflow configuration means that Coordinators can decide exactly which consumers are granted access and for how long. BitBroker will take care of all users' access and even flag users who are attempting to violate policy.
Branded developer portal | _Coming soon_... a web portal which will allow you to administer and operate a BitBroker instance via clicks in a browser.

BitBroker is a _one-stop-shop_ for policy based data sharing. As you will see in later documentation, it can be deployed in a variety of configurations, depending upon your data access and control requirements.

## Is BitBroker for me?

BitBroker is not for everyone. Sometimes, people just want to share data openly, with minimal control and in the context of open licenses. In that case, you might be able to get away with an _open data portal_, such as [CKAN](https://ckan.org/) or [Socrata](http://open-source.socrata.com/).

However, in situations where more control is required, then BitBroker can become a key enabling resource. Deploying BitBroker makes most sense when:

* You want to share data from multiple, complex and heterogeneous backend systems
* You want to share data with a wide and diverse set of people
* You want to create and deploy policies, such that only defined people, can access defined subsets, via defined routes
* You want to apply different licenses, to different people, at different times
* You want to maintain control and have the ability to rescind access at any time

BitBroker lets you build confidence with your data consumers, by enabling progressive data access as your relationship with them matures and develops.

### A modern refresh for legacy applications?

Perhaps your needs are simpler...

BitBroker is also a great solution to bring together different data sources within an organization and to present them in a simple and modern API.

Using very little effort, multiple, legacy data applications can be plugged into a BitBroker - enabling cross-siloed information sharing. Users can access this via a simple, RESTful API. You can plug the data into your favorite business applications or build an application from the range of sample applications you will find documented here.

## Ready to get started?

Here are some great jumping-off points for BitBroker:

* Try out our [interactive demos](/docs/getting-started/demo/) of a live BitBroker instance.
* Find out how to [get started](/docs/getting-started/) with your own BitBroker.
* Learn more about the [key concepts](/docs/concepts/) which underpin BitBroker.
* Explore [sample applications](/docs/getting-started/demo/#demo-applications) and [data connectors](/docs/getting-started/demo/#demo-data-connectors) to help you build your own BitBroker instance.

### Contributing to BitBroker

BitBroker is an open source project under the [Apache 2 license](https://www.apache.org/licenses/LICENSE-2.0). We welcome contributions from anyone who is interested in helping to make BitBroker better. If you would like to contribute, then you should start by reading our [contribution guide](https://github.com/bit-broker/.github/blob/main/profile/README.md) and then [get in-touch](mailto://team@bit-broker.io) with the core team.
