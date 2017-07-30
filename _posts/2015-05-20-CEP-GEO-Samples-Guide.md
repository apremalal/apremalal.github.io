---
title: WSO2 CEP Geo – Extension sample guide
date: 2015-05-20 19:55:16
categories: WSO2 CEP
---

This sample demonstrates how to filter incoming location event attributes using the geo extension isWithin function. This uses the capability of custom Siddhi functions. The output will be determined if a particular location is within the given polygon. Moreover, the output event stream is directed to a front-end web app that uses HTML5 websockets to display the received results.

### Function definition geo:iswithin(longitude,lattitude,polygon)
longitude double: longitude value in double format polygon string: polygon to be checked to see if the long, lat falls inside it; this should be passed in geojson polygon format. Return: true if the coordinate (lat,long) is within the given polygon, else false.
Note: latitude, longitude values should be in the same coordinate reference system. The query used in this sample is as follows:

```
from locationdatastream[geo:iswithin(longitude,lattitude,"{'type':'Polygon','coordinates':[]}")==true] select correlation_timeStamp, deviceId, lattitude, longitude, true as iswithin insert into iswithinstream;
```

Query Format: `from <inputstream>[geo:iswithin(<longitude>,<lattitude>,<polyg>)]`

The above query pass latitude and longitude values to iswithin function and if a coordinate is inside the polygon it will output timestamp, deviceid, latlong, iswithin to the output stream.

### Prerequisites
See prerequisites in CEP Samples Setup page. Note: Additionally you’ll need Java 1.7.* installed inorder to run this sample

### Executing the sample

Download the CEP pack from the following link: https://svn.wso2.com/wso2/interns/2013/anuruddha/geo-fencing/wso2cep-3.1.0.zip
Note: This is a modified CEP pack with the workaround for https://wso2.org/jira/browse/CEP-870

1. Add ``“org.wso2.cep.geo.GeoIsWithin”`` to <CEP_HOME>/repository/conf/siddhi.extension
Start the WSO2 CEP server with the sample configuration numbered 0113. For instructions, see Starting sample CEP configurations.
 `$ sh wso2cep-samples.sh -sn 0113`

2. Open the web browser and go to http://localhost:9763/geo-portal.

![screenshot]({{ site.baseurl }}/images/posts/main-dashboard.png)

Note: You will have to use a web-socket supported web browser since this sample uses web-sockets to communicate results with the WSO2 CEP server.

3. Select Within option from the functions drop-down menu and draw a polygon covering the area that needs to be monitored (this action will show help tip popups).

![screenshot]({{ site.baseurl }}/images/posts/functions.png)

Note: You can see the geo-json of the drawn polygon by hovering the mouse pointer on top of the polygon

![screenshot]({{ site.baseurl }}/images/posts/polygon.png)

4. Create a new execution plan using the query builder.
First give a name to the execution plan. There is a preconfigured input data stream named locationDataInputStream; make sure to select this as the import stream. Generate negation option provides the ability to get the results for within and outside the polygon. This is useful when someone needs to filter in and out events of the location data stream.

![screenshot]({{ site.baseurl }}/images/posts/query-builder.png)

Execution plan name: the name you need to assign to the execution plan Import stream: select locationDataInputStream:1.0.0 for this sample Generate negation: will generate iswithin and isoutside for the same polygon Query expression: display the generate query

5. Connect to CEP using the connect button at the top right corner of the console.

![screenshot]({{ site.baseurl }}/images/posts/connect-disconnect.png)

Executing the procedure.

Data producer is used to simulate an actual location data stream. You can add your own location data by editing <CEP_HOME>/samples/producer/location-data/resources/input.csv This csv file stores location data in the following format. <deviceid>, <time_stamp>, <lattitude>, <longitude>

6. Send desired location data by running the location-data producer.

```
$cd <CEP_HOME>/samples/producer/location-data
$ ant locationDataPublisher
```

7. You can observe the output from the web page console and there will be a marker popup showing the results.
Result format

```
Lon: <longitude of the data point>
Lat: <latitude of the data point>
isWithin: <false or true based on the point location>
query name: <execution plan name which this result is related to>
```

![screenshot]({{ site.baseurl }}/images/posts/result.png)

Use refresh icon on the left upper corner of the map to clear the current map and start over.
