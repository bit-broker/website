
---
title: Documentation
linkTitle: Documentation
cid: documentation
menu:
  main:
    weight: 20
---

Welcome to the BitBroker user guide. This guide explains all the concepts and processes that you will need to know to get started with your own BitBroker deployment.

{{% alert title="Warning" color="warning" %}}
This documentation is still work-in-progress and will be updated as the project progresses
{{% /alert %}}

## What is BitBroker?

BitBroker is an end-to-end, data sharing solution. It allows organisations to share data with third-parties in the context of robust policy definitions. These ensure that only the correct people can get access to the data, only via set access mechanisms and strictly within a defined legal framework. BitBroker lets data owners share data with confidence, so that they can leverage the expertise of external players to help analyse and realise the latent value in your data.

Services provided by the BitBroker solution are:

* **Connect heterogenous data sets**: BitBroker is a great way to bring order into an otherwise chaotic data environment. It helps you organise data from complex and heterogenous backend systems, presenting a more consistent view to data consumers.
* **Controlled data contribution**: BitBroker allows for a community of people to contribute data; however, it is not a free-for-all. You decide _who_ can contribute and _what_ they can contribute. Tell BitBroker what schematic expectations you have and it will police all contributions to make sure they are compliant.
* **Comprehensive cataloging**: Unless you ask it to, BitBroker does not take a copy of your data. Instead it indexes all the entity instances in your domain and facilitates the search and discovery of these. Policy based access is then automatically brokered via the core engine, meaning that the consumer is never aware of the source details of your data.
* **Flexible data sharing policies**: You can create and deploy any number of data sharing policies. These define what data segments are visible, how they can be accessed and the legal basis for any use. Once deployed, BitBroker will police the policy on your behalf, giving you confidence that your data is only being accessed and used as you would want it to be.
* **Reach-for data licenses**: BitBroker eases the legal burden of sharing data by allowing you to select and incorporate from a pool of common data licenses, attribution models, terms and conditions, acceptable content, etc. This means you can quickly get going with data consumers, but still with a robust legal underpinning.
* **Modern, effective suite of APIs**: BitBroker comes out-of-the-box with a comprehensive suite of APIs offering search, discovery and access to your chosen entity and time series data.
* **Complete user and key management**: Flexible key workflow configuration means that you can decide exactly which consumers are granted access and for how long. BitBroker will take care of all users' access and even flag users who are attempting to violate policy.
* **Branded developer portal**: BitBroker fades into the background and allows you to simply create, deploy and operate a branded developer portal for your user community to interact with your data.

BitBroker aspires to be a _one-stop-shop_ for policy based data sharing. As you will see in later documentation, it can be deployed in a variety of configurations, depending upon your data control requirements.

## Is BitBroker for me?

BitBroker is not for everyone. Sometimes, people just want to share data openly, with minimal control and in the context of open licenses. In that case, BitBroker is a somewhat heavy solution and you can probably get away with an _open data_ solution, such as [CKAN](https://ckan.org/). However, in situations where more control is required, then BitBroker can become a key enabling resource. Deploying BitBroker makes most sense when:

* You want to share data from multiple, complex and heterogenous backend systems
* You want to share data with a diverse set of people
* You want to create policy so that only defined people can access defined subsets and via defined routes
* You want to apply different licenses to different people at different times
* You want to maintain control and be able to rescind access at any time

BitBroker lets you build confidence with your data consumers, by enabling progressive data access as your relationship with them matures and develops.

## Ready to get started?

Why not find out how to [Getting Started](./getting-started/) with BitBroker. Or visit the [example site](https://example.bit-broker.io) and [its associated repo](https://github.com/bit-broker/example-site).
