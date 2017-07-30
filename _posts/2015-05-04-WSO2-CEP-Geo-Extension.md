---
title: Geo Extension for WSO2 CEP
date: 2015-05-04 19:55:16
categories: WSO2 CEP
---

### Introduction to Geo-spatial Analysis
Integration of geospatial data into software applications is vividly evident in today’s context, with the wide availability of GPS (global positioning system) services, especially using handheld/mobile devices. Furthermore, geospatial data is now becoming universally available through wide spreading social media networks (like Twitter, Facebook, etc). These streams of events from multitudinous sources may carry information that’s vital for better and effective decision making in today’s organizations. For example, social media event streams would be a quick and effective indicator of social unrest or an unfortunate event like a natural disaster. Geospatial data inscribed in these streams could be well used to pinpoint the geographical location of the distress cause.

### Typical use cases of Geo-spatial Analysis

* Geo-fencing
...Demarking a no-fly-zone : Detect whether the points in an even stream (typically from an aircraft) fall within a restricted territory or not
...Elephant barrier        : Check whether tagged elephants are leaving the protected area

* Transport and traffic control
...Point-based alarms          : Plot all bus halts on the map, and assign a 100m buffer to them. Alert if a certain bus is at a certain bus halt for more than its allotted time
...Movement-based alarms : Alert if 2 buses are moving in parallel

### What is WSO2 CEP and why it is useful in Geo-spatial Analysis?

WSO2 Complex Event Processor (CEP) is a lightweight, easy-to-use, open source complex event processing server available under the Apache Software License v2.0. WSO2 CEP identifies the most meaningful events within the event cloud, analyzes their impact, and acts on them in real-time. It’s built to be extremely high performing and massively scalable.

WSO2 CEP is built on the award-winning, WSO2 Carbon platform, which is based on the OSGi framework, enabling better modularity for your service-oriented architecture (SOA). The WSO2 Carbon framework contains many enhanced features and optional components to customize the behavior of the server through simple, point-and-click provisioning. WSO2 CEP is an extremely high performance processing engine that can process more than 2.5 million events per second on single-server commodity hardware. It is also massively scalable, supporting large numbers of queries via partitioning into different servers with a massive working memory support and distributed chaining of complex queries.

This makes WSO2 CEP an ideal enterprise solution for Geospatial analysis.

### Example implementation of ‘Geo-fencing’ using WSO2 CEP
The following example use case depicts how ‘Geo – fencing’ can be implemented in WSO2 CEP using its currently available facilities. Use case Consider that there is a source (beacon) that constantly emits its location data (in the form of longitude and latitude pairs) and we will use WSO2 CEP to verify if these points produced by the source is within a designated polygon or not. We will be providing you with a custom Siddhi extension to the ‘geo-within’ function, together with a UI to input/output data. In order to mimic the source, we will use a producer with an example CSV file and send over events to WSO2 CEP via HTTP. The output will be sent over HTTP back to the UI via a websocket. A detailed description of the implementation is explained in Figure 1.

![screenshot]({{ site.baseurl }}/images/posts/workflow.png)

Figure 1 shows the flow of data from source through CEP to the output UI. Here a CSV file containing latitude, longitude of coordinates, and other stream information is used as the source of the point stream that is fed to the CEP. The UI component enables the user to draw a polygon in the given map and submit a new execution plan to the CEP. The CEP will filter the event stream according to the execution plan (in this scenario it will filter out points from the input stream that are inside the polygon) and display them in the UI. Figure 2 provides a detailed explanation of each component.
This workflow mainly contains 3 components as indicated in the following figure.

![screenshot]({{ site.baseurl }}/images/posts/figure2.png)

#### Producer

Producer creates a continuous event stream to the CEP. It contains the following components as shown in Figure 3.

![screenshot]({{ site.baseurl }}/images/posts/figure3.png)

The CSV contains the input data stream with latitude, longitude and other relevant information. A CSV file with a sample event is shown in Figure 4. The event formatter transforms this event into GeoJSON format, which is the standard format in Geospatial applications. The transformed form of the event in Figure 4 is shown below. This is then fed to the CEP and the process is explained under the CEP.

```
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [
            79.852452,
            6.909472
        ]
    },
    "properties": {
        "timestamp": "06/17/2014 2:13:17 PM",
        "deviceid": "wso2deviceid1"
    }
}
```

#### Complex Event Processor

![screenshot]({{ site.baseurl }}/images/posts/figure5.png)

Figure 5 shows the data flow inside the CEP. Functions of each stage is explained below.

#### HTTP input adapter

All external data sources and data sinks use event adaptors as the entry and exit points for processing data streams, where event adaptors facilitate maintaining connections to data sources and data sinks from the CEP server. Event adaptors can be inputs or outputs. Currently, WSO2 CEP supports the following input adapter implementations, but users can also write custom input adapters. In this example, we use an HTTP input adapter. In the body of the message the location data is sent as a GeoJSON point type.

#### Event Stream

Event streams are another key part in the CEP. All the processing happens based on streams. Here, event streams need to be defined as WSO2 Event Stream Definition. The stream definitions used in the current example is as follows.

```
<streamDefinitions>  
   <streamDefinition>  
    {
    "name": "locationDataInputStream",
    "version": "1.0.0",
    "nickName": "",
    "description": "",
    "correlationData": [
        {
            "name": "timeStamp",
            "type": "STRING"
        }
    ],
    "payloadData": [
        {
            "name": "deviceId",
            "type": "STRING"
        },
        {
            "name": "lattitude",
            "type": "DOUBLE"
        },
        {
            "name": "longitude",
            "type": "DOUBLE"
        }
     ]
    }
   <streamDefinition/>  

   <streamDefinition>
   {
    "name": "isWithinOutputStream",
    "version": "1.0.0",
    "nickName": "",
    "description": "",
    "correlationData": [
        {
            "name": "timeStamp",
            "type": "STRING"
        }
    ],
     "payloadData": [
        {
            "name": "deviceId",
            "type": "STRING"
        },
        {
            "name": "lattitude",
            "type": "DOUBLE"
        },
        {
            "name": "longitude",
            "type": "DOUBLE"
        },
        {
            "name": "iswithin",
            "type": "BOOL"
        }
     ]
    }   
   </streamDefinition>
</streamDefinitions>
```

#### Input event builder
An event builder converts different input event formats such as WSO2Event, XML, Map (Key-value pairs), JSON into a uniform format that is supported by the CEP or any other event consumer. It handles input mappings for the CEP. In the current example, the following event builder configuration has been used. It takes the HTTP message body from the input event adapter and parse the GeoJSON point data into the input event stream.



```
<eventBuilder>
   <from eventAdaptorName="httpInputAdaptor" eventAdaptorType="http">
      <property name="topic">locationInStream</property>
   </from>
   <mapping customMapping="enable" type="json">
      <property>
         <from jsonPath="$.properties.timestamp" />
         <to default="0" name="correlation_timeStamp" type="string" />
      </property>
      <property>
         <from jsonPath="$.properties.deviceid" />
         <to default="0" name="deviceId" type="string" />
      </property>
      <property>
         <from jsonPath="$.geometry.coordinates[0]" />
         <to default="0.0" name="lattitude" type="double" />
      </property>
      <property>
         <from jsonPath="$.geometry.coordinates[1]" />
         <to default="0.0" name="longitude" type="double" />
      </property>
   </mapping>
   <to streamName="locationDataInputStream" version="1.0.0" />
</eventBuilder>
```

#### Output event formatter

Similar to the event builders on the input side, an event formatter helps convert the uniform WSO2Event in to different event formats other than WSO2Event, such as XML, Map, JSON and Text. This component is responsible for output mapping. In this example, the output event formatter converts the outstream into a JSON (more specifically, a GeoJSON) stream.

#### HTTP output event adapter

Similar to the input event adapter in the input side, output event adaptors are used to send events to event sinks. By default, the CEP provides the following output event adaptor implementations: email http jms kafka logger mysql sms soap wso2event In this example, an output HTTP adapter is used. It takes the GeoJSON output from the event formatter and puts it in the body of the message. This will then be sent to the UI via a websocket server.

## CEP execution plan

### Siddhi query language (SiddhiQL)

SiddhiQL is designed to process streams and identify complex event occurrences in WSO2 CEP. Siddhi queries describe how to combine existing event streams to create new event streams. When deployed in the Siddhi runtime, Siddhi queries process incoming event streams, and as specified by the queries, they generate new output event streams if they do not exist. Siddhi also supports custom codes within queries. You can implement Windows, Transformers, Output Attribute Processors, Functions (conditions and expressions) in a pluggable manner using the current implementation. In order to implement the Geofencing example, we have used the custom function and custom transformer facilities in SiddhiQL.

Custom function: geo:iswithin(latitude,longitude,polygon)

A custom function was used to carry out the geofencing process. This function will take the longitude and latitude values of an event, and it will determine if that event is within a designated polygon or not. Following are the parameters used in the custom function.

latitude double    : latitude coordinate in double format
longitude double : longitude value in double format
polygon string     : polygon to be checked to see whether the lat, long falls inside it; this should be passed in geojson polygon format.
Return : true if the coordinate (lat,long) is within the given polygon, else false.

Note: latitude, longitude values should be in the same coordinate reference system.
Execution plan

WSO2 CEP uses execution plans to store event processing logic. An execution plan is bound to an instance of the Siddhi complex event processing runtime, which is responsible for the actual processing of the events. The event processor allows users to configure multiple execution plans thereby providing multiple isolated event processing environments per execution plan. Once the above custom function is deployed, the following execution plan can be used for the current example. From the provided UI, users can change this execution plan, and provide new values to the parameters, such as input/ output stream implementations, geo:iswithin parameters, etc.

## code
<executionPlan xmlns="http://wso2.org/carbon/eventprocessor" name="isWithinCheckExePlan" statistics="enable" trace="enable">
   <description />
   <siddhiConfiguration>
      <property name="siddhi.persistence.snapshot.time.interval.minutes">0</property>
      <property name="siddhi.enable.distributed.processing">false</property>
   </siddhiConfiguration>
   <importedStreams>
      <stream as="locationdatastream" name="locationDataInputStream" version="1.0.0" />
   </importedStreams>
   <queryExpressions><![CDATA[from locationdatastream[geo:iswithin(lattitude,longitude,"{'type':'Polygon','coordinates':[[[79.85173402706208,6.909744100877063],[79.85442696491334,6.91094765233452],[79.85471664348692,6.9097654026993816],[79.85173402706208,6.908796168811299],[79.85173402706208,6.909744100877063]]]}")==true] select correlation_timeStamp,deviceId,lattitude,longitude, true as iswithin insert into iswithinstream;]]></queryExpressions>
   <exportedStreams>
      <stream name="isWithinOutputStream" valueOf="iswithinstream" version="1.0.0" />
   </exportedStreams>
</executionPlan>


NOTE: Custom transformer

When considering the geospatial data, the coordinate reference system (CRS) on which the data is collected becomes very important. Therefore, the Geofencing function should be able to convert the geospatial data into a common CRS. This can be achieved with the use of a custom transformer in the WSO2 CEP. In the current example, it is assumed that the polygon and the longitude, latitude pairs are all in the same CRS. In an event where the CRSs are different, the following is the code of the custom transformer used instead.

![screenshot]({{ site.baseurl }}/images/posts/ui.png)
Consumer and the UI

A UI panel allows the user following functionalities
* Draw a polygon in the Map and extract that polygon in GeoJSON format.Here we have used Open Street Maps for this application. If you want to change the map it can be easily achieved by editing the geoclient.js file
* Create a new execution plan in CEP.
* Open a socket connection between CEP and UI.
* Display the CEP output stream.

This consumer UI is entirely written with Jaggery. This was possible because of the extensibility of the WSO2 Carbon platform. Jaggery UI and the WSO2 CEP server communicates via HTTP, SOAP and Websockets.
