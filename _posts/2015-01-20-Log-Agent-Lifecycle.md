---
title: Log Agent Lifecycle
date: 2015-01-20 19:55:16
categories: WSO2
---

This is the follow-up blog post og WSO2 log analyzer tool, introducing the log agent lifecycle.

We are going to adapt the following way of distributing configurations among log publishing agents.

There are two modes which a log agent can be configured. User have to define this mode beforehand, default is the “client based config mode”, there can only be a singe mode for an agent at a given time.

1. client config mode – users will configure the log streams from client side. (classical logstash way)
user can define the configurations in agent.conf [1]

2. Managed config mode – user doesn’t have to configure stream specific configurations at the agent side instead user should define the log-groups which it needs to get configured on. managedagent.conf [2] This mode is useful for a large cluster of nodes, as user can perform all the configurations at a central location.

Following sequence diagram shows how the managedconfig mode behave.

![screenshot]({{ site.baseurl }}/images/las/las-agent-lifecycle.jpeg)

Followings are few possible use-cases explained in Q&A manner.

1. What will happen if the user chose to switch the configuration mode?
– This will make the previous configurations obsolete and will always honor the latest config mode.
2. How can we distinguish agents?
Based on the agentID defined by the users. Users can make use of instance privateip/publicip to generate unique names, ip will be picked at the run time and replace the id accordingly (agentid : “esb-${privateip}”). Final agentID will have the following format. agentid : “–“, this master generated ID is used to make sure the uniqueness of the agentID.
3. What will happen if the defined agent group is not already configured?
– A new log-group will be created in the master side with empty configurations. No logs will get published since there’s no configurations.
4. Is it possible to add/delete log-groups to an agent from the master side?
– yes, once agent registered in master, all the stream specific configurations can only be done at the master side.
5. managedagent.conf will get read only once in the agent life-cycle, once the agent establish a proper connection with master all the configurations will be handled from there. If the user change the managedagent.conf and restart, it won’t get affected to the existing way the agent is configured.

Feel free to raise any other use-cases which I have missed here.
