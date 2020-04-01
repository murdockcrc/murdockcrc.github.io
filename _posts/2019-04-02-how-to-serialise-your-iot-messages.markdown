---
layout: post
title: How to serialise your IoT messages
date: '2019-04-02 20:16:00'
tags:
- iot
---

One of the first questions that pops up during our IoT engagements is: _how should we serialise messages from our IoT devices?_ Althoughnobody has ever questioned serializing messages using a JSON structure, it is not always clear what schema those JSON messages should take.

We typically recommend customers to not reinvent the wheel, and this time is no exception. &nbsp;[CloudEvents](https://cloudevents.io/)and adopt it as the data structure to serialize their IoT messages.

## Why CloudEvents?

- It is a project under the stewarship of the Cloud Native Computing Foundation. On top of that, the working group for CloudEvents has members from some of the world's heaviest weights in computing, including Microsoft, Alibaba, Amazon Web Services, Google and [many others](https://github.com/cloudevents/spec/blob/master/community/contributors.md). This gives you certainty that the project stands on solid technical grounds. These providers have already started adopting the specification, which means you can benefit from out-of-the-box features too (see how Azure Event Grid [already supports](https://docs.microsoft.com/en-us/azure/event-grid/cloudevents-schema)CloudEvents natively).
- CloudEvents offers support for transport protocols bindings which are common in the IoT world, such as [AMQP](https://github.com/cloudevents/spec/blob/v0.2/amqp-transport-binding.md), [MQTT](https://github.com/cloudevents/spec/blob/v0.2/mqtt-transport-binding.md)and [HTTP](https://github.com/cloudevents/spec/blob/v0.2/http-transport-binding.md)transports.
- There are [multiple SDKs](https://github.com/cloudevents/spec/blob/master/SDK.md)already offered in many languages, which will speed up your project as you don't have to write your own serialization libraries.

The CloudEvents project also provides a spec for [JSON messages](https://github.com/cloudevents/spec/blob/v0.2/json-format.md). This is probably the core asset of the project. In my experience, agreeing on a common data model early on in your IoT project is key. Yet it is quite difficult to reach this agreement. Getting this understanding with your device engineering team, your cloud development team and your data science team is essential to avoid unnecessary refactoring, confusion and frustration between the teams.

The data model you choose should be flexible to accommodate your multiple device types, their variations and their different generations. Still, it should also enforce a data structure that will allow all players in the game to process and interpret messages in a coherent way. In essence, these two requirements contradict each other and it is hard to achieve the correct balance between them. This is perhaps the trickiest point in trying to agree on a data model. With CloudEvents, I recommend that you try to take the spec as-is, as this will allow you to benefit from all the artifacts and integrations the project will deliver over time. However, if you do find in your project a blocker which does not allow you to fully embrace the CloudEvents spec, then I suggest you still take a look at it as a source of inspiration for your own message models.

For illustration, here is a variation of a data model which we used in the past. Some values (property names in the JSON body) have been modified for anonymity.

<!--kg-card-begin: markdown-->

    {
        "specversion" : "0.2",
        "type" : "com.customerId.iot.deviceType.eventType",
        "source" : "device_id",
        "id" : "B234-1234-1234",
        "time" : "2018-04-05T17:31:00Z",
        "customer_extension_01" : "value",
        "customer_extension_02" : {
            "otherValue": 5
        },
        "contenttype" : "application/json",
        "data" : [
            {
                "signal_name_01": "temp_sensor_01",
                "values": [24.01]
            },
            {
                "signal_name_02": "temp_sensor_02",
                "values": [25.02]
            },
            {
                "signal_name_03": "alarms_controller",
                "values": [true, false, false, true]
            }
        ]    
    }

<!--kg-card-end: markdown-->

Note that, as per the spec, the _data_property is simply a native [JSON object](https://tools.ietf.org/html/rfc7159#section-4), so you have the flexibility to represent whatever data model and hierarchies you need. Typically, it is a good idea to stick with an array of JSON objects, each object having the same data structure as the rest to avoid complex parsing rules. With the example message above, you can add event-based signals, polled-based signals, alarms or notifications to your message, and be able to keep more or less the same structure across the board. It's a quite versatile data structure.

Furthermore, this data structure is good for devices sending only one signal (with an array of 1 element) or for devices sending thousands of signals in the same message.

The structure also gives you the option to add _extensions_(the properties called _customer\_extension\_xx_), meaning you can extend the message as it flows through your pipeline (for example, add a _field gateway timestamp_as the message flows through your IoT gateway).

## Challenges of the approach and their mitigation

I've often heard this approach be critized as being too verbose, and thus inefficient in terms of bandwidth costs. For example, each object in the _data_array has the property names _signal\_name\__ and _values_repeated over and over again, possibly even thousands of times for very large messages. This is a valid concern, which can be mitigated in the following ways:

- The impact of this verbosity will only be felt if your IoT project reaches very large &nbsp;scale. For projects just getting off the ground, this verbosity will not be an issue.
- You can serialize your JSON messages at the edge using something like MessagePack, which will significantly reduce the size of your messages.
- You can use compression on your messages, such as GZip/Deflate.
- You can combine the two options above (MessagePack + Compression), achieving very attractive compression ratios.
- All of the above has the purpose of enabling you to keep a **self-descriptive, semantically-rich** data structure, while at the same time achieving efficient transmission costs. Trust me, you want your messages to be self-descriptive, otherwise you'll fall in model-version-management hell and that is a problem whose complexity will erase the savings you achieved in bandwidth costs.

## Test Drive

Let's take this approach for a test drive. We'll take the above JSON message as a benchmark. We'll start by serializing that JSON string into a _byte[]_, then we'll serialize that byte array with MessagePack, and we'll finally use GZIP to compress the MessagePacke message. We'll compare the size of each byte array, and then we'll deserialize the whole thing back to a _string_to confirm the correctness of data.

Here's the code to execute the experiment:

<!--kg-card-begin: markdown-->

    using System;
    using System.Text;
    using MessagePack;
    using System.IO.Compression;
    using System.IO;
    
    namespace compress
    {
        class Program
        {
            private static string _messageSample = @"
                {
                    ""specversion"" : ""0.2"",
                    ""type"" : ""com.customerId.iot.deviceType.eventType"",
                    ""source"" : ""device_id"",
                    ""id"" : ""B234-1234-1234"",
                    ""time"" : ""2018-04-05T17:31:00Z"",
                    ""customer_extension_01"" : ""value"",
                    ""customer_extension_02"" : {
                        ""otherValue"": 5
                    },
                    ""contenttype"" : ""application/json"",
                    ""data"" : [
                        {
                            ""signal_name_01"": ""temp_sensor_01"",
                            ""values"": [24.01]
                        },
                        {
                            ""signal_name_02"": ""temp_sensor_02"",
                            ""values"": [25.02]
                        },
                        {
                            ""signal_name_03"": ""alarms_controller"",
                            ""values"": [true, false, false, true]
                        }
                    ]    
                }";
    
            static void Main(string[] args)
            {
                var messageInBytes = Encoding.UTF8.GetBytes(_messageSample);
                Console.WriteLine($"Plain JSON object size: {messageInBytes.Length}");
                
                var messageAsMessagePack = MessagePackSerializer.FromJson(_messageSample);
                Console.WriteLine($"MessagePack object size: {messageAsMessagePack.Length}");
    
                using (FileStream fileStream = File.Create("compressed.bin"))
                using (GZipStream compressionStream = new GZipStream(fileStream, CompressionMode.Compress))
                {
                    var memStream = new MemoryStream(messageAsMessagePack);
                    memStream.CopyTo(compressionStream);
                }
    
                using (FileStream fileStream = File.Open("compressed.bin", FileMode.Open))
                using (FileStream decompressedfile = File.Create("decompressed.txt"))
                using (GZipStream decompressStream = new GZipStream(fileStream, CompressionMode.Decompress))
                {
                    decompressStream.CopyTo(decompressedfile);
                }     
    
                using (FileStream fileStream = File.Open("decompressed.txt", FileMode.Open))
                {
                    var deserialized = MessagePackSerializer.Deserialize<object>(fileStream);
                    Console.WriteLine(MessagePackSerializer.ToJson(deserialized));
                }                                             
            }
        }
    }

<!--kg-card-end: markdown-->

This code produces the following output:

_Plain JSON object size: 1004MessagePack object size: 400{"specversion":"0.2","type":"com.customerId.iot.deviceType.eventType","source":"device\_id","id":"B234-1234-1234","time":"2018-04-05T17:31:00Z","customer\_extension\_01":"value","customer\_extension\_02":{"otherValue":5},"contenttype":"application/json","data":[{"signal\_name\_01":"temp\_sensor\_01","values":[24.01]},{"signal\_name\_02":"temp\_sensor\_02","values":[25.02]},{"signal\_name\_03":"alarms\_controller","values":[true,false,false,true]}]}_

Furthermore, the size of the compressed stream is now reduced to 293 bytes.

Our original message had a size of 1004 bytes. Using MessagePack and GZIP compression, we have shrunk that down to a mere 293 bytes! And we still have a semantically-rich, self-descriptive message, while achieving significant bandwidth costs reduction using efficient serialization and compression. SWEET!

<figure class="kg-card kg-image-card"><img src="/content/images/2019/04/image.png" class="kg-image"></figure>