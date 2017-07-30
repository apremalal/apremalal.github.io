---
title: Architecturing a Log analysis tool
date: 2015-01-16 19:55:16
categories: WSO2
---

Today I am going to blog about a new tool I’m going to involved in development at WSO2.

Here is a high level overview of the main components of the log analysis tool.

### Log publishing agent
* These agents will connect to the analysis server (which can be considered as the master) – agents will be installed on the host machine with only a minimal configurations where it has information about the master and, authentication information
* Master will list down the available agents, and will provide a wizard to perform the configurations – Here master is the central point to distribute configurations to the clients, this is similar to the puppet approach of configuring clients, a group identification configuration can be used in a clustered scenario where all the members of the grupId will receive a similar set of configuration.
* Configurations wizard will walk the user through two main stages

1. Input configurations
  * Initially this will be a file source, configurations related to file input will be provided here.
  * Moving forward this will be expanded to a logstash kind of approach [2] , where users can define different kinds of sources. (Ex : syslog, twitter, websockets, etc.. )
2. Filtering configurations
   * At this stage user can configure, how should the log events to be filtered/ what sort of filters needs to be applied to the event. These filters will apply a regex pattern/s in to the event and extract fields. Pre-configured set of filters will be provided which can be applied to generic log events like log4j, apache httpd.
   * Also, user can search and pick a sample event from the previously configured file input and extract formats by highlighting the fields and defining regex patterns.
   * Output format of the filtered event will be a json which contains user defined fields along with a field of the raw event (see the sample [1]).
   * Single agent can have multiples of the above kind of configuration (file{}, filter{}, output {}) in order to publish more than one source events.

### Search
Search will primarily be based on Lucene queries.
Results will be shown in a tabular view where users can drill down further using the extracted fields.
There will also be an option to apply time constraints to the search query.
Relative to current time – Ex : Last 12 hours, Last 5 days
There will be a side panel which will have stats about the event fields.
Once a field is clicked user will get a pop up pane with the top matches related to that field (field is basically a regex pattern), with the frequency of occurrence both as a percentage and a count.
### Dashboards and Reporting
The popup described in the search section will also have a section for Reporting and Dashboards based on metrics like; Average over time, Top values, Maximum over time, Minimum over time, Top values, Top values by time, Rare values, etc…
There will be two types of dashboards, Lucene query based and real time (Siddhi query based). Idea is to provide a unified language for both the real time and historic searches as improvements.
Users will be provided with the option to export reports & dashboards in CSV, PDF, Excel formats.

### Alerts
* Both the real time and Lucene searches can be used to create alerts. (save as alerts)
* Alerts can be configured based on three main categories
  * Per-result alerts – Based on a continuous real-time search (Siddhi).
  * Scheduled alerts – Runs a search according to a schedule that user specifies when creating the alert. (Periodic Lucene queries)
  * Rolling window alerts – Based on a continuous real-time search happens in a user defined time window (Siddhi)
* Can have multiple trigger actions based on the following trigger conditions
  * Number of results
  * Number of Hosts
  * Custom search
* Trigger actions.
  * Send email / SMS / Phone call
  * Web hook – POST to a user defined web service with the alert payload.
  * Run a custom script

[1]
  ```
    {
        "message" => "127.0.0.1 - - [11/Dec/2013:00:01:45 -0800] \"GET /xampp/status.php HTTP/1.1\" 200 3891 \"http://cadenza/xampp/navi.php\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:25.0) Gecko/20100101 Firefox/25.0\"",
     "@timestamp" => "2013-12-11T08:01:45.000Z",
       "@version" => "1",
           "host" => "cadenza",
       "clientip" => "127.0.0.1",
          "ident" => "-",
           "auth" => "-",
      "timestamp" => "11/Dec/2013:00:01:45 -0800",
           "verb" => "GET",
        "request" => "/xampp/status.php",
    "httpversion" => "1.1",
       "response" => "200",
          "bytes" => "3891",
       "referrer" => "\"http://cadenza/xampp/navi.php\"",
          "agent" => "\"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:25.0) Gecko/20100101 Firefox/25.0\""
}
```

[2] https://www.elastic.co/guide/en/logstash/current/config-examples.html
