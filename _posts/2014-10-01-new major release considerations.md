---
title:  "New major release considerations"
date:   2014-10-01 17:51:48
tags: SAML
author: tmilos
---

It’s been a couple of months since the first release of the [SamlSpBundle](https://github.com/aerialship/SamlSPBundle) and almost
a year since the first release of [AerialShip LightSaml](https://github.com/aerialship/lightsaml) library. Feedback and community
engagement has been better than expected, thank you all, and many pull requests has been accepted. However, many issues
and new ideas come to the sight. Also, I learned a lot about SAML and have now much clearer understanding of it. Thus, I’m considering
writing a new major release, with new features, where old issues and misguides will be fixed.

## Own organization

LightSAML initially was released within the [AerialShip](https://github.com/aerialship) organization as a hobby. In the meanwhile
it got more serious and outgrew that organization, and requires it’s own space to grow further. So, in next major release it will be
branded just as **LightSAML** and moved to own [LightSAML](https://github.com/lightSAML/) organization.


## SAML Profiles

Major feature add-on to the SAML Core in next major release will be implementation of the SAML Profiles. Right now, the core
lightsaml library does support SAML Profiles. The SamlSpBundle does implement Web Browser SSO for SP, but has complex and
inflexible configuration. Misunderstanding of the Single Logout (SLO) SAML Profile drove the design of the SSO session in the
wrong way, which also affects SP SSO. And finally, profile implementation in the bundle makes small benefit for non-Symfony users.

Thus, the core library will implement the SAML Profiles, mainly SSO and SLO, both for SP and IDP.


## Validation

Current library misses proper validators for the strict SAML requirements, and the next major release will implement some of them.


## Core, SP vs IDP, and SSO vs SLO

Core data models, bindings, serialization/deserialization, validation, four different profiles, Symfony bundles, integration with
other frameworks and apps… Quite a big scope for a single library. Not sure yet how, but in new major release it will be divided into
smaller more consistent parts, relevant to a single usage usage case. Common core cases like SP SSO will remain MIT licensing, but some
exotic cases like IDP might be released under GPL.



## Generalization and Flexibility

On one hand, the core implementation of the SAML Profiles must implement the profile flow by SAML specification and thus must depend
on some services, but their implementation and configuration must be flexible, so it both can satisfy novice users with out-of-the-box
functionality and advanced heavy lifting customizations for complex use cases.


## Symfony bridge and security

Of course, having listed all said above, the current implementation of the SamlSpBundle can not remain the same. After all, it has it’s
own flaws in the complexity and inflexibility of the configuration. So, in the major release, it will not implement any SAML
functionalities, but will be based on the core libraries, only implementing the Symfony security firewall. For sure, it will support
through its configuration all of the core library flexibility.

