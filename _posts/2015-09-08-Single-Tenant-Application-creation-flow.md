---
title: Appfactory Single Tenant Application Creation Flow
date: 2015-09-08 19:55:16
categories: WSO2 AppFactory
---
Concept of adding single tenant support is to provide isolated deployment environments. Earlier the challenge was we had to spawn entirely a new instance to facilitate this and it was poorly utilizing the resources. With new stratos 4.1.0 we make use of kubernetes as the IaaS which give us the capability to have containerized environments. The main advantage of containers in terms of single tenant apps is the better resource utilization where all the containers share the same OS and hardware resources while providing isolated deployment environments.

![screenshot]({{ site.baseurl }}/images/appfactory/SingleTenantApplicationSupport.png)

#### Nginx Extension
​Nginx extension listen to the network topology of the iaas and adopt nginx configurations accordingly to route request to pods. In kubernetes all the created pods will get assigned a unique port with the same IP (this might be different depending on the number of nodes )

#### DNS service (dnsmasq)
Additionally we have introduced a dns service to the development setup to map single tenant hosts to nginx.

![screenshot]({{ site.baseurl }}/images/appfactory/AppFactorysingletenantURLflow.png)

Reason behind this is that for every new single tenant pod, Stratos creates a new service based on the pod id and every pod gets a unique port(per node). So without a Load Balancer and a dns service, it’ll be a manual process to navigate to applications deployed in a pod.
