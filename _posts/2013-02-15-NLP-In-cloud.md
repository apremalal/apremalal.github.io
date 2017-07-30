---
title: Preparing a GATE application to cloud
date: 2013-02-15 19:55:16
categories: NLP
---

Today most of the NLP applications require web compatibility .GATE provides a good support for cloud based developments with spring frame work.We can build the GATE application using the developer interface and import it to the cloud very easily.Let’s get started.Open the GATE developer and close all the previous applications that are being opened by GATE developer. Right click on Applications > Create New Application >  Corpus pipeline . Then load the ANNIE system with defaults using the toolbar option. ANNIE defaults contains following.
* Document Reset PR.
* English tokenizer.
* Sentence splitter.
* POS Tagger.
* Gazetteer.
* NE Trancducer.
* Orthomatcher.

Now you got a basic NLP application with ANNIE default engine.

![screenshot]({{ site.baseurl }}/images/posts/gate_app.png)

Make sure to organize the programming resources according to the order shown in the figure.
To export as a cloud app , Right click on the application and choose export for GATECoud.net and save it.
In my next post I’ll show how to bring this basic application to the actual cloud environment.
