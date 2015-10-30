---
title:  "AerialShip LightSaml 1.0.1 released"
date:   2013-12-04 11:17:03
tags: SAML
author: tmilos
---

The [AerialShip LightSaml version 1.0.1](https://github.com/aerialship/lightsaml/releases/tag/1.0.1) is released. You can get it’s
source code from [Github repository](https://github.com/aerialship/lightsaml) or through
[composer](https://packagist.org/packages/aerialship/lightsaml) aerialship/lightsaml.

It supports some of SAML 2.0 data models: AuthnRequest, and Response, and HTTP POST and Redirect bindings, thus providing
basic means for Single SignOn. It's been tested with sample XML messages obtained through communication of
[SimpleSAML](https://simplesamlphp.org/) with [ADFS](https://msdn.microsoft.com/en-us/library/bb897402.aspx), that all
are available in the resources/sample directory.

The library does not include nor implement any SAML profile. At the moment, profiles are out of the scope of the library
and up to the users to implement.

This PHP library is open source and released under MIT license, and for the details you can look at the included
[LICENSE](https://github.com/aerialship/lightsaml/blob/master/LICENSE) file.

Happy federation!
