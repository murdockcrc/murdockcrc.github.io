---
layout: post
title: No, a Swiss datacenter provider is not safer than a cloud provider
date: '2019-10-05 15:53:26'
tags:
- cloud
- privacy
- law-enforcement
- switzerland
---

I recently read a LinkedIn post from a Swiss engineer, claiming that he would never put data into any public cloud provider and instead rely on the privacy and security protection of a Swiss-based datacenter, which must also be operated by a Swiss-based legal entity.

This is a frequent argument I hear in the country: they fear that the US government can access data from Swiss customers via legal tools such as the Patriot Act and the CLOUD Act. According to this point of view, the privacy of &nbsp;data in the hands of an American company is not safe. Data must be hosted in a Swiss-based datacenter, operated by a Swiss company with no ties to the United States.

This opinion needs to be challenged. It is misguided, misinformed and ultimately a foul play that intends to profit from people's fear.

**Is my data safer in a local datacenter, operated by a local company?**

It isn't. With the proliferation of cyberattacks in our moden society, the investments datacenter operators need to do in security far outreach their budgets. Today, the world is full of state-sponsored actors that crawl cyberspace with extremely sophisticated &nbsp;tools and attack procedures. Successfully defending from such sophisticated attackers requires enourmous recurring financial investments and staffing your datacenter with highly-skilled cybersecurity specialists. Recruiting these people is extremely hard and public cloud providers are in a much better position to aquire this talent. If a sophisticated attacker wants your data, they will most certainly find their way into the data of your local datacenter.

To give you an illustration about the magnitude of the problem, Microsoft invests 1 billion US dollars **per year** on security (1). Your local datacenter provider does not come anywhere near that financial firepower.

**What about Patriot Act and CLOUD act? Will the US government have access to my data if it is hosted in a public cloud?**

This is a very complicated topic, but one thing is for sure: US law enforcement agencies repeatedly try to gather customer data directly from the American companies which host it. Not only that, but they typically serve a gag order to the tech company, meaning that the company cannot inform their customer that a law enforcement agency has requested their data. Eventually, this led Microsoft to repeatedly sue their own government. In Microsoft's opinion, US agencies do not have sovereignty on data of non-American customers which resides in a datacenter outside the border of the United States. Requests to access this data for criminal investigations should follow established procedures for the exchange of information between jurisdictions. The US government disagreed and multiple lawsuits have been fought, including at the level of the Supreme Court of the United States(3).

Countries can exchange information through a process called MLAT (Mutual Legal Assistance Treaty). Through the use of MLAT's, the US has already had an instrument to get your data from your Swiss datacenter operator (this MLAT is [HERE](https://www.rhf.admin.ch/dam/data/rhf/strafrecht/rechtsgrundlagen/sr-0-351-933-6-e.pdf)). However, US law enforcement complained that the MLAT process is too slow, and hampers the process of gathering evidence in a criminal case.

As a response to the mess created by these data requests from US law enforcement, and the difficult position tech companies had to assume, the CLOUD (Clarifying Lawful Overseas Use of Data) Act was passed in 2018. With the CLOUD act, government agencies can request data from a tech company wherever it resides, but the companies keep the right to reject a request if such a request is deemed to violate the laws of the country where the data is stored.

The Zurich-based law firm Homburger produced a professional opinion about this, with the following conclusions(2):

- CLOUD Act applies only to criminal cases.
- It requires a court order.
- It's compatible with the Cybercrime Convention of 2012, which also applies to Switzerland.

In summary, the US government already had international legal instruments to force your local datacenter provider to hand over your data. The CLOUD Act does not make your position weaker than before.

**Can we trust cloud companies to reject a request for information?**

Under the CLOUD Act, companies have the right to reject a request that is deemed to violate the laws of the affected foreign country. Can we trust these companies to give us this protection? I believe so. These companies have a compelling commercial interest in protecting their customer data. It is good for their international business. They have a good record of acting in our privacy's best interest. And they have proven to be ready to go to battle against their own government in the courts of law to protect the privacy of their customers. Microsoft publishes a report called _[Law Enforcement Requests Report](https://www.microsoft.com/en-us/corporate-responsibility/law-enforcement-requests-report)_. In there, you can see that during the second half of 2018, Microsoft rejected 17% of such requests. Facebook also produces such a report [HERE](https://transparency.facebook.com/government-data-requests).

The topic of data privacy is a complex one, and it is far from being solved. However, we must not confuse the challenges of today's complex, interconnected world and believe that our own little datacenter is the solution to modern privacy and cybersecurity challenges. It is not, and I contend that this mentality actually exposes you to larger cybersecurity risks and offers no additional privacy protection. On top of that, refusing to use public cloud services just puts you at a disadvantage in terms of innovation capacity, agility and speed, but that's a topic for another post :).

**Sources**

1. Brad Smith, Carol Ann Browne: "_Tools and Weapons"_, pg. 111.
2. Homburger, David Rosenthal: "_Banken & Co. in die Cloud_", May 29th 2019. [https://media.homburger.ch/karmarun/image/upload/homburger/BJ7qKhG0V-2019-05-29\_Banken in die Cloud\_ROD.pdf](https://media.homburger.ch/karmarun/image/upload/homburger/BJ7qKhG0V-2019-05-29_Banken%20in%20die%20Cloud_ROD.pdf)
3. United States v. Microsoft Corp. [https://www.scotusblog.com/case-files/cases/united-states-v-microsoft-corp/](https://www.scotusblog.com/case-files/cases/united-states-v-microsoft-corp/)
