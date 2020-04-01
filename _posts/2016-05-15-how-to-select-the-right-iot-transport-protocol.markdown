---
layout: post
title: How to select the right IoT transport protocol
date: '2016-05-15 09:55:00'
tags:
- iot
---

This week during the [Integrate 2016 event in London](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjgioSs9tnMAhWBI8AKHXpkDLUQFggdMAA&url=http%3A%2F%2Fwww.biztalk360.com%2Fintegrate-2016%2F&usg=AFQjCNGNdy3-pxRP0wmdIqavJ_piFqlpSw), several Microsoft Product Group members took the stage to provide insights about the Microsoft strategy and roadmap for application integration. We have blogged about these topics [here](http://www.codit.eu/blog/2016/05/13/integrate-2016-day-2/) and [here](http://www.codit.eu/blog/2016/05/12/integrate-2016-day-1/).

Upon taking the stage, Clemens Vasters highlighted the importance of adopting AMQP for device telemetry. AMQP is the clear direction being given by Microsoft moving forward. Azure ingestion services support both AMQP and HTTP, but AMQP should be your default go-to choice.

Anyone experienced in what goes under the hood with HTTP on the networking layer knows how tremendously inefficient the protocol is. Let us not forget that HTTP was built and designed to enable the transfer of Hypertext between clients and servers, an operation that is intrinsically one-sided (connections are always originated from the client) and stateless. Real-time, bi-directional communication via HTTP was been implemented with two common workarounds: polling and, more recently, WebSockets. HTTP was not built for efficient messaging, which is the scenario that we are after in IoT scenarios. On the other hand, AMQP has been built to enable efficient transmission of many payloads of small sizes. For technical details about AMQP, see Clemens Vasters webscast series [HERE](http://m.youtube.com/watch?v=ODpeIdUdClc).

Support for AMQP is not mainstream as with HTTP. The following slide, presented during Integrate, shows you the availability of protocol SDKs and APIs across different stacks. This should make the selection of the right transmission protocol straight-forward.

<figure class="kg-card kg-image-card"><img src="/content/images/2019/04/image-4.png" class="kg-image"></figure>

So what if you are programming on a stack that does not have an AMQP implementation available? This happened to me while working on an iPhone app that transmitted about 1 event per second to an Azure Event Hub. Making an HTTP post per event every second is tremendously expensive, ineffective and murders the battery of the device. Yet, as of the time of this writing, there is no AMQP library for Obj-C or Swift that can be used with Azure Event Hubs or IoT Hub. Luckily for us, Event Hubs allows you to batch events. So if you don’t require real time streaming of data from your devices, this is a convenient solution to this problem. My solution to the problem consisted on:

- Implement the telemetry generator as an Observable sequence (using [Reactive X](http://reactivex.io/) extensions).
- Each time the observable fires an event, append that event into a static array.
- Fire off an event when the length of the event equals 100 (this is an arbitrary number).
- Copy the first 100 elements from the array into a new array, serialize those array elements and post them to Event Hubs as a batch HTTP request.
- If the request is successful, delete the first 100 elements from the original array (since we transmitted them already, I don’t need them in the app anymore).
- If the network transaction fails, you don’t delete the items in the array and their transmission will be retried later.

This solution is very effective and clean to implement using Observables. Observable extensions are available in many different languages and I encourage you to look at this approach.

