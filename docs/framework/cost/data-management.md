---
title: Data management for cost optimization
description: Describes some of the decisions you may need to make when optimizing your data footprint for cost.
author: david-stanford
ms.date: 10/21/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: 
---

# Data management for cost optimization

Describes some of the decisions you may need to make when optimizing your data footprint for cost.

## Optimize data transfer

Bandwidth refers to data moving in and out of Azure datacenters. Most of the time inbound data transfers (data going into Azure datacenters) are free. For outbound data transfers, data going out of Azure datacenters, the data transfer pricing is based on Billing Zones.

## Data retention and archival

In typical systems, not all data needs to be available for online processing. Data no longer needed can be removed, freeing up underlying storage.

## Use the right type of data store and partition or scale it appropriately

Choosing an inappropriate data store or one that is mis-configured can have a huge cost impact on your design and you can save tens of thousands of dollars by getting the data tier correct.

Consider storing binary image data, whilst it is possible to store it in Azure SQL Database as a varbinary(MAX) column type, you should strongly consider storing the data in Azure Blob Storage Block Blobs and if your design requires SQL, then store a lookup table in SQL Database, and retrieve the document on the fly to serve it to the user in your application middle tier.

This way the majority of your data capacity is consumed with fast, cost-effective blob storage that is a good few orders of magnitude cheaper than Azure SQL Database, but where needed – your use of SQL Database is highly targeted to high-speed data lookups and set-based operations, where SQL really shines. This pattern is called polyglot persistence and it doesn't just apply to PaaS services, consider that Azure ‘Hot' Block Blob Storage costs around 1/50 of the cost of the equivalent size of the Premium SSD volume you would be placing the database on, and this model still applies even in an IaaS world.

You **don't need to make all of your decisions up-front** and cost-design is more than just an app-dev concern, many Azure services can be used to enhance applications ability to scale dynamically, even where they may not have been originally designed to do so.

For example, many asp.net stateful web applications can be made stateless (and hence can be auto scaled for a dramatic cost-benefit) by using [Azure Redis Cache](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-aspnet-session-state-provider), or Cosmos DB as a back-end session state store via a [Session State Provider](https://github.com/aspnet/AspNetSessionState).

Consider also, that you can reduce load on servers that repeatedly serve the same content, intelligent use of CDN and caching services can dramatically decrease load on front-end servers that are continually rendering dynamic content which doesn't change frequently. Every render cycle of a page or payload consumes both compute and memory, and with CDN you trade this off for pre-canned storage and bandwidth costs, and the savings can be dramatic, especially for static content services (such as JavaScript Single-Page apps and media streaming content), refer to the [Static Content Hosting Pattern](../../patterns/static-content-hosting.md).

Think about infrastructure costs too, for example consider using https for securing content transmission of a service instead of VPN-based traffic transmission which requires a VPN Gateway, note that this is a specific optimization that should be considered in the wider security context, as well as a cost design consideration.

In both cases, the traffic is encrypted and passing over a public network. In the first case (https) your cost for encryption and decryption is absorbed into the web service costs, in the second case (VPN) the cost is clear and discrete, but is additional to the web service itself.

It is important to not view the cost component of technology architecture design as a one-off consideration. While it does hold an important place in Cloud design principles for new or migrating capabilities, you should also establish controls, business processes, and a regular cadence for reviewing your Cloud spend. This approach will minimize reactive, unplanned cost optimization drives/efforts/initiatives.

While you may already have processes in place for reviewing I.T. costs, there are some factors to managing Cloud costs that are unique, even when compared to other "as a service" utility spends. The flexibility of your Cloud architecture, the business benefits of your Cloud spend and the opportunities from new Cloud capabilities must all be taken into consideration.

Costs are –

- **An operational metric** An increase in cost can be an indicator of a problem, such as a code change leading to a dramatic increase in chargeable CPU cycles. If left unchecked, costs can scale out of proportion to the resulting business benefit, profit, or transaction volume.

- **A success indicator**.  An increase in cost can be an indicator of an increase in demand or sales, especially if the Cloud applicating is autoscaling to maintain performance. In this case, the cost can be proportionate to or even less than the resulting business benefit, profit, or transaction volume.

Therefore, a bigger bill is not always indicative of a problem that needs to be fixed. The Cloud is a fundamental facilitator of business success: giving an organization the ability to quickly take advantage of new opportunities and the flexibility to quickly scale to meet demand. Is the cost increase in alignment with a greater volume of customer transactions or website traffic? Is the ratio of costs vs customer transactions acceptable? Sometimes it's not easy to make a direct correlation between increased cost and increased business benefit but having an awareness of business initiatives or business performance can help.