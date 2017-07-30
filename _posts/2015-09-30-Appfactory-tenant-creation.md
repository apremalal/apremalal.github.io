---
title: New governance and tenant creation changes to AppFactory deployment
date: 2015-09-30 19:55:16
categories: WSO2 Appfactory
---

With AppFactory moving to a single Stratos, there are some changes to tenant creation as well as resource creation. Following diagrams[1] shows how the existing tenant creation and registries being shared among three different cloud environments..

![screenshot]({{ site.baseurl }}/images/appfactory/Appfactoryresourcestorywithnewstratosdeployment.png)

Here in the existing governance model, there are separate databases for config, governance and userstore for three cloud environments. Tenant creation as well as cartridge subscription happens in all three Stratos instances by listening to message broker topic. Separate governance instances were introduced to offload resource management from Stratos.

![screenshot]({{ site.baseurl }}/images/appfactory/ProposedAppfactoryresourcestorywithnewstratosdeployment.png)

With the new single Stratos deployment [2] followings are the proposed changes.

Stratos share appfactory userstore and governance. Hence there’s no need to create tenant in Stratos side.
enant creation happens inside dev, test, prod greg; by listening to message broker topic
There’s an additional message going to MB for cartridge subscription, and it happens in Stratos side.
With the above changes it’s important to notice that the tenant creation and cartridge subscription happens as separate processes
