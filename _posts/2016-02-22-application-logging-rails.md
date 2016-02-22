
---
layout: ryan
title: "Scalable Application Logging in Rails"
description: ""
category: 
tags: []
---

# Application Logging in Rails

I am a big fan of logging. I have found that when debugging a problem that is occurring in production, having good logs can often make a very difficult fix turn into a trivial one. The downside to this approach is that the traditional approach Rails uses of writing logs to a flat file quickly becomes a bottleneck.

Rails does however provide some good built in logging tools which we can extend. By default rails writes logs to a flat file which is typically named after the environment. The problem is that as soon as we need any analytics on our logs it becomes difficult. Hand crafting regular expressions and pulling out the wanted data is often the way people handle this. We can do better.

### Logs
Writing logs is not always trivial. When an application has errors and writes to the log, it's essential to know the severity of the problem. Rails ships with a handful of log levels:

~~~ruby
[:debug, :info, :warn, :error, :fatal, :unknown]
~~~

Generally I associate each warning level with its anticipated response time by the development team.

##### Log Levels vs. Developer Response Time
unknown == We do not use this level.
debug == No response
info == No response
warn == Look into within a week
error == Look into within 24 hours
fatal == Look into ASAP

When deciding what information to include in the log message, I find ensuring the severity, timestamp, user id, device information, a description why this log is being written, and at least a partial backtrace essential.

### Tagged Logging

We can only account for these levels in our own application code, we cannot ensure that Gem creators handle logs with our timeframes in mind. To handle this we tag all of the main applications logs. Rails has tagged logging built right in with [ActiveSupport::TaggedLogging](http://api.rubyonrails.org/classes/ActiveSupport/TaggedLogging.html). 

Tagged Logging is very useful as it also supports nesting. With a modularized codebase it is very useful to log each module with its own tag. This allows us to very easily setup queries in Kibana which include the smallest amount of noise and find what we are looking for very quickly.


### Logging Pipeline 

We can setup a robust logging system which will be able to scale easily. The following setup is a common one in production environments with Rails applications at scale. The goal is to get our rails application logs into Kibana. To do this we need to use Logstash. In order to write to logstash we can use logstash-logger.

1. We use the Gem [logstash-logger](https://github.com/dwbutler/logstash-logger) which writes application logs directly to logstash in JSON.
2. Logstash populates Elasticsearch
3. Kibana reads from Elasticsearch and provides great querying and visualizations

~~~bash
Rails Application -> Logstash -> Elasticsearch -> Kibana
~~~

### Result
With this logging pipeline we now have real time application logging data right in kibana where we can easily query. This setup will scale when the application requires more than a single machine as well. At my current employer [Kudos](https://kudosnow.com) we have developed this setup and it has significantly enhanced the way we debug our application.





