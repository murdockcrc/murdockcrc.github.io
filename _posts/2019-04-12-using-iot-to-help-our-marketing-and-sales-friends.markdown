---
layout: post
title: Using IoT to help our Marketing and Sales friends
date: '2019-04-12 20:14:00'
tags:
- iot
- digitalization
---

For the last 3 years, IoT has been a term hyped to no end by the industry and the media (together with Big Data and Blockchain, amongst others). Hyped technology concepts are good because they create awareness outside of its starting niche community, but it also has the drawback of creating skepticism. IoT is no exception to this phenomenon, especially since many organization have invested significant amount of capital in the technology without delivering value back to their organisations.

So today, I want to present a real, **tangible** case of how we used IoT to create value for marketing and sales. Due to contractual restrictions, I won't name the customers, but we've done this twice at with customers.

## How equipment manufacturers make money

The scenarios involve industrial equipment manufacturers. &nbsp;This equipment is static, that is, it rarely changes its physical location.

Typically, manufacturers produce the equipment, which is then bought by a _reseller_. The reseller then takes the equipment to a geographical location of its competence (a sales territory), sells it there to the end-customer, and provides after-sales support and services. Think about this as the way you purchase a Toyota. A dealer (who is responsible for a specific geography), imports the car into its territoty, sales it to the consumer, and provides support and warranty services. Under this model, the manufacturer does not even know who the end customer is. This _indirect distribution channel_is very common among equipment manufacturers. The manufacturer only knows it sold 100'000 devices to the Japanese reseller, but does not know more than that.

## The value of equipment location

Wouldn't it be nice if our manufacturer could know the exact location of its equipment? It turns out that this information is tremendously valuable for marketing and sales purposes.

If we know, for instance, that out of these 100'000 equipment sold in Japan, most of them are sold in Osaka, you would be able to create targeted, data-driven marketing decisions to support and establish a position in that specific market. Without this data, you might be tempted to assume that Tokyo is your biggest market, but maybe it isn't. So instead of organizing a brand-specific event in Tokyo, using equipment location data you would decide to invest your marketing money where your most loyal customers are: Osaka.

What about sales? If we could determine the location of the equipment, we could monitor the trend of on-boarding and off-boardings on a per-sales district basis. We can spot trends in activations: if we detect that on-boardings of devices in Osaka haven been declining for the last 3 consecutive months, you will have the evidence to prove that your Osaka market is under attack from the competition (perhaps your local reseller got a better deal from a competitor and is pushing their product instead of yours). Without this data, your Japanese sales manager will never be able to react in time to this sales assault, your sales guys are basically flying in the dark and being blindsided by a disloyal reseller.

From the technical perspective, the two use cases I have just presented may seem very trivial. Yet, they are of enormous value to your sales and marketing managers. Knowing where your devices are physically located will allow you to make data-driven marketing decision (instead of speculation or gut-feeling). This capability will also allow you to keep a tight, near-real time pulse on your sales dynamics, allowing you to promptly react to real (instead of _perceived_) threats.

## How do we get there

To achieve this capability, our implementations have typically looked like this:

- Your devices must have a connectivity capability. How you achieve this is irrelevant, but your devices must be able to connect to the Internet and to Microsoft's Azure IoT Hub.
- Once your devices are able to establish a network connection to your IoT Hub, we will configure the Hub to publish _Connect/Disconnect_ events to us. This means that, every time any of your devices connects or disconnects, we will receive an automatic notification from the Hub. This capability was added this year by Microsoft, and you can read more about it [HERE](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-use-metrics-and-diags).
- We implemented a process that listens to those _Connect/Disconnect_events in batch mode (every x minutes). These events have &nbsp;a specific [schema](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-monitor-resource-health#understand-the-logs), and publish multiple data points. One of them is the _maskedClientIpAddress_. Microsoft will disclose to use the public client IP address the device used to connect to the Hub. However, Microsoft will not disclose the last octet of the IP address. This is to protect the privacy of the end client. However, this is good enough for our purposes. Here's how these events look like:
<!--kg-card-begin: markdown-->

    {
    	"records": 
    	[
            {
                "time": " UTC timestamp",
                "resourceId": "Resource Id",
                "operationName": "deviceConnect",
                "category": "Connections",
                "level": "Information",
                "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
                "location": "Resource location"
            }
        ]
    }

<!--kg-card-end: markdown-->
- Once we get the masked IP address from the Hub (together with the device Id, which is also part of the message we get from the Hub), we send that IP address to a geo location service. IP geolocation is a third-party service which transforms a public IP address into a geographical location, like this:
<!--kg-card-begin: markdown-->

    {
        "status":"success",
        "description":"Data successfully received.",
        "data":{
            "geo":{
                "host":"www.google.com",
                "ip":"74.125.29.147",
                "rdns":"qg-in-f147.1e100.net",
                "asn":"AS15169",
                "isp":"Google Inc.",
                "country_name":"United States",
                "country_code":"US",
                "region_name":"California",
                "region_code":"CA",
                "city":"Mountain View",
                "postal_code":"94043",
                "continent_name":"North America",
                "continent_code":"NA",
                "latitude":"37.419200897217",
                "longitude":"-122.05740356445",
                "metro_code":"650",
                "timezone":"America\/Los_Angeles",
                "datetime":"2015-05-22 09:10:35"
            }
        }
    }

<!--kg-card-end: markdown-->

In the sample above, we used a geolocation service to transform the public IP address _74.125.29.147_into a physical location. For our purposes, we don't need to know an exact address. The city, municipality or district is good enough.

- Once we get this information from the service, we save the data to the device's digital twin, allowing us to make it visible for historical analysis to our sales and marketing friends.

We implement this as a fully-automated process using an Azure Logic App. So basically, you can achieve this location-gathering capability with pretty much zero programming skills (except for the connectivity of your devices to the Hub, which does require some effort from your device engineering team).

Since we are storing this information in our own database, we can then use the power of Azure Maps to create [heatmaps](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps)for marketing and sales. _Business people love maps!_ And they love it for a reason: it allows them to boil down large amounts of data into simple visualisations which are easy to interpret, act on and share.

## Caveats

I've found this solution to be very powerful and valuable to our customers. However, there are some caveats to be aware of:

1. You depend on your end-customer providing connectivity to your equipment. Typically, manufacturers enable this networking by leveraging the end customer's WLAN/LAN or using a bluetooth connection to a smart phone app. However, this assumes the end customer will onboard the device into his network. If he doesn't do that, you'll never get any data. The alternative to that is to embed a connectivity module into your hardware and have it transmit data autonomously. However, this increases the production cost of your device.
2. Since Microsoft does not disclose the last octet of the client IP address, we need to make an assumption and substitute the "xxx" in the last octet with any other number between 1 and 254. This change may throw off the accuracy of the geolocation service. In practice though, since knowing the city where the device is connecting from is typically good enough for our objectives, this is not a real problem.
3. If the users of your equipment are large B2B organizations, bear in mind that the actual exit point to the Internet might be very far away from the actual location of the equipment (typical case are satellite offices connected via an MPLS network and using a centralized Internet exit point). If these are edge cases within your customer base, then you are good to go.
4. Resellers can become very skeptical of you once they know you are trying to learn more about their end-customer. From a business perspective, this is a tricky balance to strike: you don't want to depend so much on resellers and you want to know who your customer is. At the same time, resellers are the ones that bring money to you today and you need to protect that relationship.

## Conclusion

What have we achieved so far? We know the location of the equipment we are selling. But we still don't know who the end customer is. We don't know how often they use the equipment, or for what purposes. We still don't know how often the equipment fails (remember, after-sales support is provided by the reseller. They will fix equipment that broke down and very rarely do they tell you about that). Essentially, we still know **nothing** about the actual buyer of our equipment. We are still so very far away from knowing anything about the end customer.

But this little piece of information we do now know (the equipment's location) is so very powerful. We removed speculation from marketing and allow the department to make data-driven decisions based on geographical facts about equipment location. We allow sales to closely monitor the sales estate on a sales district basis. We are able to automatically keep a pulse on our equipment and its location, and spot trends in sales. These trends allow us to identify threats, disloyal resellers, the effectiveness of sales campaigns on specific locations, and bring data to the sales management process.

