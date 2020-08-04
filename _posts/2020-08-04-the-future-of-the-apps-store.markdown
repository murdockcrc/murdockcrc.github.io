---
layout: post
title: 'The future of the App store'
date: '2020-08-04 00:00:00'
summary: 'The App store  has become too big. It is time to rethink what its value is, and what to expect of its owner'
tags:
- AppStore
- Apple
- Monopoly
published: false
---

<figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2020/08/ww_app-store-badge_150909.png" class="kg-image"><figcaption>.</figcaption></figure>

In the last couple of weeks, there's been a couple of high-profile cases where companies have denounced the behavior of Apple and its operation of the App Store.

[Hey.com exec says Apple is acting like ‘gangsters,’ rejecting App Store updates and demanding cut of sales](https://www.theverge.com/2020/6/16/21293419/hey-apple-rejection-ios-app-store-dhh-gangsters-antitrust)

[Spotify files EU antitrust complaint against Apple](https://www.reuters.com/article/us-apple-spotify-tech-eu-idUSKBN1QU18G)

[ProtonMail founder: Apple uses monopoly to “hold all of us hostage”](https://arstechnica.com/tech-policy/2020/08/protonmail-founder-apple-uses-monopoly-to-hold-all-of-us-hostage/)

Let's image a hypothetical scenario:

# It's 1998

_Microsoft announces a change in their operating system, Windows: starting with the next version, all applications (back then,  the term 'app' did not exist) need to be installed using the Microsoft Store. Distributing Windows software by copying/sharing .exe and compressed files is not allowed anymore, and the operating system will prohibit such installations of software. Furthermore, developers who monetize their software will need to pay a Microsoft a 30% fee._

If  Microsoft would have indeed made such a decision, the outrage would have been of epic proportions. The DOJ would have probably won its anti-trust case of 1999. Having Microsoft do something like that would have been outrageous, yet a smaller company managed to do just that in 2008.

# What is the value of the App store?
When Apple introduced the App Store in 2008, the concept actually made sense.

## Protects against malware
Everyone knew that Windows' way of distributing software (by floppy disks/CDs and copying installation files offline) was the perfect ground to distribute malware. The App Store promised to be a security gateway, where Apple controlled that every application was developed in a secure way.

## Protects the user experience
By forcing developers to adhere to basic usability principles, the App Store also made sure that applications didn't suck too much. 

# Imagine

Data science teams will typically work very independently from DevOps teams. They collaborate with domain experts for the field they are researching, but their data research is mostly conducted outside the scope of IT or software development teams. Eventually, the data science team finishes its job and they have a model which is ready to be operationalized (meaning it's ready to be launched into production, either as a standard software service or embedded into another product). This step from "data science" to "software operations" can be quite tricky.

Data science teams typically do not operate the software they produce, because they are not an operations team (this is especially true if their models need to be embedded into someone else's product). But DevOps teams are usually very skeptical of this "throw over the fence approach". Common reasons for DevOps teams to offer resistance are:

* The software is not modular. As a software artifact, the code (typically Python) suffers from poor software architecture.
* The data science team is not making proper use of security credentials and secret management.
* The code has no consideration for cross-functional requirements, such as scalability, security, monitoring, logging, error handling, retry policies, amongst others.

On the other hand, data science teams find the collaboration with external DevOps and IT teams quite frustrating too. Common reasons are:

* Lack of support for common DS tooling. Particularly, IT departments are quite skeptical of having staff with unmanaged clients such as Macs or Linux devices, thus forcing the DS team to either a) use Windows PCs to do their work (which they hate) or b) enforcing a ton of security restrictions when using non-vetted computers (which makes the DS team unproductive).
* Constant push-back from DevOps teams when they are trying to release their software (for the reasons mentioned earlier).
* Lack of corporate support for the technology stack they prefer (such as exotic databases, obscure reporting tools or uncommon hosting choices). And when I say "exotic, obscure uncommon", I mean that from the perspective of enterprise IT.

Whatever camp you choose to support, I believe both perspectives are correct. DevOps teams' critique on the software artifacts delivered by DS teams is typically true. But here is the first mismatch in expectations: Data scientists are data scientists. They are not software architects. They are not software security experts. They are not experts in designing distributed systems. They are not experts in operating software, so don't expect them to produce code which solves your needs for logging, alerting and monitoring. Upon delivering a software artifact, they have done their job: they have done their science and their software artifacts reflect that outcome. Is that software ready to go into a production environment? Hell no. But this should not be the expectation towards them either.

Similarly, data science teams should understand that their primary mandate towards the organization is to delivery science. As such, decisions like hosting runtimes, databases, security and such should not be their responsibility. This should be considered as a opportunity to focus on the team's core strengths. As an example: more often that not, the answer from a DS team to operationalize a model is to "put the Python code in a AWS VM" or "put it in a container inside a Kubernetes cluster". DS teams usually have the expectation that they own the product of their work, and as such, they should have the liberty to choose how to bring it into production, and should have free choice about the tech stack to use. This is also an expectation misalignment.

# Moving to a collaborative approach

The fundamental problem is a misalignment of expectations. DevOps teams expect DS software to be mature and ready for production. They expect for the software to be compatible for the corporate tech stack. They expect the software to have strong monitoring, logging, security and alerting. On the other hand, DS teams expect for them to have free-reign on the entire lifecycle of their solutions.

The most effective way to bring data science into real-world products is to adopt a collaborative approach. Understand that data science does not work on a silo, but that it is rather one components of modern product development. Product Development is a multi-disciplinary effort, of which data science can be a part of. As such, instead of having separate data science teams, infrastructure teams, development teams and operations teams, your organization should take a product-centric approach instead. Software developers and architects should provide a consultative approach to data scientist, so that their software is born already adhering to best practices from the start. Similarly, security and infrastructure specialists should provide a consultative approach so that data scientist understand the security policies to access corporate resources (such as production databases), understand how to leverage company-wide secret management technology, and understand which key metrics the code must publish in order for the operations team to monitor it.

Data science teams should be willing to let their baby go. A software architect from the company might rip the code apart and make it modular, so that it is easier to maintain, test and scale. This is fine, and as a data scientist, you should be happy that someone is bring software architecture maturity into your code. DS teams should stop fighting about putting everything into a VM. Operations teams and solutions architects are in a much better position to choose the correct runtime for the code (provided you explain to them the computational requirements or the models). Also, the operations team is probably right when they don't let you use the cool-database-of-the-year to host your data. There are considerations such as backup and restore, logging and auditing, scale, support contracts (or lack thereof) which makes your favorite open source database not suitable for a production system inside your organization.

# The result

In my experience working as a bridge between DS teams and DevOps teams, I've found that resetting expectations and putting a collaboration framework in place really helps. Friction and conflict are reduced, and time-to-market is accelerated. And overall, every benefits from a having more mature software:

* Leveraging existing database and infrastructure assets makes DS teams faster, as they can leverage existing investments.
* Putting DS code in source control makes collaboration between data scientists and software engineers more effective.
* Bringing testing to DS code increases the reliability of the product.
* Automating the building, testing, and release of DS code using DevOps tooling increases the productivity of the team and the reliability of the product.
* Ultimately, the software evolves into something which the DevOps team feels confident they can operate within the constraints of a production environment.