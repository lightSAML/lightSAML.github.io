---
    title: LightSAML Core
    aside: aside-lightsaml.html
    active: about
---


LightSAML Core is a [PHP](https://php.net/) library implementing [OASIS](https://www.oasis-open.org/standards#samlv2.0)
[SAML 2.0](http://saml.xml.org/saml-specifications) protocol, fully OOP structured with DPI principles, reusable, and
embeddable.

LightSAML Core implements and supports:

 * SAML data model classes: Metadata, Protocol, Assertion
 * Serialization/deserialization of data model to/from xml
 * XML security and certificates
 * SAML bindings: HTTP POST and Redirect
 * SAML profiles: Web Browser SSO for SP
 * Simple [Pimple](http://pimple.sensiolabs.org/) bridge for customizing core services and profile flows

Following is supported by LightSAML, but not in Core but in some other component:

 * Web Browser SSO IDP profile - covered by [LightSAML-IDP](/LightSAML-IDP)
 * Symfony Bridge & integration - covered by [Symfony Bridge](/Symfony-Bridge) and [SP Bundle](/SP-Bundle)
 * Web Browser SLO profile - covered by [LightSAML-Logout](/LightSAML-Logout)

Following at the moment is not supported:

 * SOAP and Artifact bindings
 * Custom extensions

LightSAML Core is covered with unit tests and CI:
[![Build Status](https://travis-ci.org/lightSAML/lightSAML.svg?branch=master)](https://travis-ci.org/lightSAML/lightSAML)
[![Code Coverage](https://scrutinizer-ci.com/g/lightSAML/lightSAML/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/lightSAML/lightSAML/?branch=master)

LightSAML tends to publish quality code
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/lightSAML/lightSAML/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/lightSAML/lightSAML/?branch=master)

LightSAML is open source and distributed under [MIT licence](https://github.com/lightSAML/lightSAML/blob/master/LICENSE).
