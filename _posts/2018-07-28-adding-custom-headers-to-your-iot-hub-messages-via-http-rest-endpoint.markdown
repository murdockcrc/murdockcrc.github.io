---
layout: post
title: Adding custom headers to your IoT Hub messages (via HTTP REST endpoint)
date: '2018-07-28 09:42:00'
tags:
- iot
---

_Please do yourself a favor and try to always work with IoT Hub’s client SDK’s._

However, if for some reason you are forced to work with the REST endpoints, here is a tip to include custom message properties into your IoT Hub messages.

## How to add custom message properties

To send device-to-cloud (D2C) messages to IoT Hub via the HTTP REST endpoint, you need to POST to the `http://{your_iothub_fqdn}/devices/{device_id}/events/messages` endpoint.

To add a custom header, simply add an HTTP header prefixed with `iot-app-`.

**Sample request:**

<figure class="kg-card kg-image-card"><img src="https://cdn-images-1.medium.com/max/1600/1*O-PWO8QBGZPZZ0kF2dhyIg.png" class="kg-image"></figure>

**Output**

<figure class="kg-card kg-image-card"><img src="https://cdn-images-1.medium.com/max/1600/1*hM_82MHSdGbOmQLko9OoZQ.png" class="kg-image"></figure>

You will find the custom properties in the `applicationProperties` property of the IoT Hub message.

**Why are message properties useful?**

When you send a message to IoT Hub, in principle, what you are sending is a byte array of information. Sometimes you need to add properties to your messages to facilitate processing. For instance, you might want to tag a message as being an `alarm`, and such messages will get treated with priority. If you are sending your messages in plain JSON, you could simply stick that additional property in the JSON body. But maybe you don’t want to do that, because:

1. You don’t want to “pollute” your messages with additional properties, which by nature, don’t belong in the message body.
2. You don’t want to unwrap the message to read these properties (that slows the ingestion pipeline down).
3. You can’t add such properties to the message because you are not sending JSON to IoT Hub (you might be sending compressed payloads, batched messages, Avro-encoded messages, XML, or raw byte arrays).

Another use case for properties is the use of IoT Hub routing rules. Although IoT Hub recently added the possibility of making routing decisions based on the message’s body, we don’t advise customers to do this for the following reasons:

1. This forces IoT Hub to deserialize your payload, which adds latency to the message ingestion process.
2. This won’t work unless your messages are all serialized in plain JSON.
