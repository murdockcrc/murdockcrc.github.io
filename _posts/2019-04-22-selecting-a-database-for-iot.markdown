---
layout: post
title: Selecting a database for IoT
date: '2019-04-22 19:23:52'
tags:
- iot
- azure
---

_This is a blog post series focused on IoT_

- _Part I: Introduction (this post)_
- _[Part II: Ingesting data](https://blog.delgado.ms/ingesting-time-series-data/)_
- _[Part III: Querying data](https://blog.delgado.ms/querying-time-series-data/)_

Selecting a database which is fit for IoT scenarios is a difficult task to do. It is particularly problematic for data architects with long experience with a particular database engine. Yet, just because you've been using a hammer all your life doesn't mean that every problem you face is a nail. Selecting a proper database for your use case requires open-mindedness and exploring options outside of your traditional comfort zone.

In my experience, a database for IoT should at least have the following characteristics:

## Optimised for large-scale time series reads

Access to your data is typically in the form of a time series. What is a time series, and why is it different from other forms of data sets?

Time series describes the changes of a system across time. This is a very important characteristic of IoT data, and it will deeply impact our selection of a database. Time series data is characterised by the following three components (more [here](https://docs.timescale.com/v1.2/introduction/time-series-data)):

- Time-centric: time will be the main dimension on which you will be querying your data. The correctness of your result set will be dependent on the accuracy of the timestamps you have provided to your messages. For more on this topic, please see [THIS](https://blog.delgado.ms/5-ways-to-better-manage-datetime-in-iot-solutions/) other blog post.
- Append-only: IoT data is a perfect fit for event sourcing. More on that later.
- Sensitivity to latest events: users will typically care more about the time series data for the present and the most recent past. The older data gets, the less valuable it is for the user. Think about it in this way: will you care to know the temperature of a sensor right now? What about that same value, 2 years ago? Data which is far away in the past is typically only useful for data scientists trying to run experiments on historical data. For the normal IoT user, old data has less value. This might sound trivial, but it will play a key role in the cost of your solution. This is because it does not make sense for you to store all your data in expensive storage (read SSD disks). A database optimized for IoT will give you the option to offload older data sets into cheaper storage options.

Typically, users are not interestd in a specific data point for a specific device at a specific point of time. They are typically interested in trends, aggregations, time-series data (give me all messages from _deviceId01_ for the last week). This is a very different access pattern than what most OLTPs and other database systems are optimised for.

### The (typical) IoT data access patterns 

Typical queries that we see in the field are the following:

**Last Values:** for any specific _deviceId_, retrieve the last value sent. Examples of this query are:

- Get the _OperatingHours_ of a device. Devices, especially industrial equipment, typically keep a counter of the hours that they have been in continuous operation. For remote monitoring scenarios, you typically care about the value after the equipment's last reboot, so you are looking to retrieve the last value that device has sent.
- Remote Monitoring dashboards: these will typically present the user with the _current_ state of devices, meaning the last value they have transmitted for specific KPIs.
- Alarms: although the history of alarms is important, users will typically be concerned with the current status of alarms. In particular, they want to know if there are alarms which are currently active, and since when are they active. This is another "get me the last value" scenario.

**Time series** : this is the stereotypical graph that shows the historical values of signals transmitted by a device. This time series can be broken into two types:

- Raw time series: for example: "_for_ deviceId01_, give me all temperature measurements for the last 24 hours. Visualize the data where the_ x _axis represents time and the_ y _axis represents the values_".
- Binning: bins allow you to make sense out of very large numbers of time series data points. This is one of the most important features a database must provide to you. Suppose the following ask from a user: "_for_ deviceId01_, give me all temperature measurements for the last 3 months. Visualize the data in a histogram_". Suppose that _deviceId01_ is sending temperature measurements every 10 seconds. Asking for the last 3 months of data means retrieving 777'600 data points and throwing them in a dashboard. There is no way any user will be able to make sense out of so much data. Bins allow you to do the following: "_for_ deviceId01_, give me all temperature measurements for the last 3 months. Group the result in bins of 24 hours and, for each of those 24-hour periods, give me the average, mix and max._" Bins allow the system to summarize the data for the user, and provide projections (like _min_ and _max)_ so that the user can identify anomalies in the bins (binning on averages will unfortunately smooth out anomalies and hide them). With this technique, you can show the user a graph with 3 different plots: one for the avg of the bins, one for the max values inside the bins, and one with the min. You can certainly choose other aggregation functions for your bins.

**Counters:** counters are a common aggregation measure. For example: "_give me the total number of times that_ alarmX _was activated for_ deviceId01 _in the last 30 days_".

Of course, your users will throw many other different queries at you. However, the queries mentioned above are the absolute minimum you will need to support, especially if your IoT solution is focused on remote device monitoring. Predictive maintenance scenarios will have different query profiles, usually more sophisticated than the ones mentioned above. So make sure your db has a very rich feature set and query semantics. This will allow you to grow as your IoT solution matures and users ask for more sophisticated capabilities.

## Optimised for large-scale ingress of data

IoT can quickly generate millions of messages per day. This obviously depends on the size of your IoT landscape, but in general, you can expect an IoT platform to handle very large message ingress. This can be a challenge for traditional RDBMS systems, since optimizing for _writes_ often means sacrifing _read_ performance. Your database must be able to support constant ingress of many messages, yet also provide timely-access to the telemetry data.

## Optimised for Event-Sourcing pattern

IoT lends itself for event sourcing. This is because:

- You don't have to change telemetry data. The data that arrives in the database will never be changed. There is no point in changing it. Sensor measurements are what they are at a specific point in time and you'll never need to change their values. Therefore, your database does not need to support _Update_ operations on its data. This makes it easier for db vendors to optimise the database for large ingress without compromising the performance of reads. Resource locks are not needed. We call these _append-only_ databases.
- Your db does not need to support transactionality. The success or failure of storing a message from _deviceA_ does not depend on the success of storing any other message. IoT telemetry messages are independent from each other and you don't have to implement transactionality when storing them. Eliminating this constraint also gives db vendors the freedom to make design choices that facilitate large data ingress and fast query performance.

So databases which are optimised for transactionality, atomicity, editability are typically poor choices for IoT scenarios. This is because implementing these features &nbsp;forces the db vendor to make important compromises which will affect performance.

## Offered as a managed service

This might be a contentious topic, specially for those of you skeptic of the public cloud. Yet, I would never advise a customer to deploy a database for IoT on-prem, particularly if you're expecting a very large estate of IoT devices. Taking a database from a cloud provider will save you months in terms of time-to-market, as you won't have to design the networking, compute and storage for a large on-prem db cluster. You can get all these from a cloud vendor in a matter of minutes. Sizing the infrastructure components of a high-scale, distributed database can be quite hard (think about designing to provide the right disk I/O latency. Not an easy problem to solve).

## Distributed architecture

Due to the large scale of data ingress, and the complexity of time-series queries, your db must be able to scale horizontally across multiple nodes. This already renders many traditional RDBMS systems ineligible, as they commonly find it very hard to implement cost-effective distributed architectures.

## Be flexible with schemas

Your IoT devices will have many different types of messages. You will have to support multiple generations or families of devices, each one having its own particular data model. Reaching a common data model is extremely hard to do, as that typically requires you to deploy changes in the software of your existing field devices to accomodate data model standardisation. This is impractical and expensive. Your IoT db does not have to be strictly schemaless, but it should be able to work with flexible data structures on the fly.

## Provide deduplication

When designing IoT systems, you should assume _at-least-once_ delivery of IoT messages from your devices. You must be prepared to handle message duplication. The transmision of messages from your devices might fail due to transient errors. Devices might retry to send messages that were already sent to the backend. Your database should have operators to retrieve only distinct messages. This is typically done by having devices transmit every message with a _messageId_ property (we use a &nbsp;UUID value for that purpose), so the db can filter for distinctiveness.

## Test Drive

To bring these recommendations to practice, this blog series will focus on two databases which are optimised for time series analysis, and quite common on IoT scenarios: [Azure Data Explorer (ADX)](https://azure.microsoft.com/en-us/services/data-explorer/) and [Timescale](https://www.timescale.com).

I have excluded InfluxDb because I can't find a cloud-native managed service for it. I have also excluded AWS Timestream as I don't have access to it on a trial account. I will also not cover Druid since I lack the technical knowledge to evaluate it properly. However, all the code used to run my experiments on Timescale and ADX is available on [GitHub](https://github.com/murdockcrc/timeseries-loader). I highly encourage AWS, Influx and Druid specialists to run the same experiments on those platforms and report the results on the Disqus comments section. I'll extend the contents of this blog series with your findings too (with author credit, obviously).

Why did I choose ADX and Timescale? I have been working with ADX for almost 18 months. I have been working with it even before it was called Azure Data Explorer, and even before it was an Azure service available for preview. I have had the pleasure of working closely with the Microsoft ADX engineering team, and I have witnessed first hand how powerful this database is.

Why Timescale? Timescale is an extension based on the popular PostgreSQL database. Most of it is open source. Timescale counts very large production deployments under its belt, and has produced fantastic results in benchmark analysis on both writes/sec and complex query performance. Whereas ADX is a Microsoft-propietary database available only in the Azure cloud, Timescale on PostgreSQL is OSS portable across clouds (or even on-prem). It is important for me to also offer the open-source flavor to this analysis.

This is a blog series. In the second post, I'll focus on the process of ingesting data into both databases.

In the third and final post, I'll focus on the process of querying the data based on the data access patterns specified above.

Stay tuned.

_Comment below if you think there is another key database requirement missing from the list above._

