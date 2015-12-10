---
title:  "Symfony 3 support"
date:   2015-12-08 13:11:14
tags:   LightSAML, Symfony
author: tmilos
---

[Symfony 3.0.0](http://symfony.com/blog/symfony-3-0-0-released) has been released these days, and LightSAML has been
patched to support it. Thanks to [Charles Bell](https://github.com/cb8) who initiated it and gave the contribution. At
the same time, Symfony v2.3 is kept as the lowest supported version, so if any of you has some legacy you still could
use the new LightSAML.

LightSAML Core
--------------

The [lightsaml/lightsaml](https://packagist.org/packages/lightsaml/lightsaml) had compatibility issues only with the
metadata building command due to console dialog/question helpers. Issue was resolved by removing the command out of
that core library. Since the library already implements
[SimpleEntityDescriptorBuilder](https://github.com/lightSAML/lightSAML/blob/049df678a5871a97f995cac8043bba759d12a9bb/src/LightSaml/Builder/EntityDescriptor/SimpleEntityDescriptorBuilder.php)
the command itself look a bit deprecated. Eventually, if the need arise, a separate lightsaml cli tool project could
be initiated.

Beside that console dependency, LightSAML-Core depends on symfony/http-foundation and symfony/event-dispatcher. There
where no compatibility issues, and all remained the same. No [PSR-7] adoption was made, and there are no plans to do
it so far, until some concrete need or requirement.


LightSAML Symfony Bridge
------------------------

In [LightSAML Symfony Bridge](https://packagist.org/packages/lightsaml/symfony-bridge) no significant changes were done
in order to support Symfony 3, but it suffered few modifications to support Symfony 2.3. Those were issues with injecting
http request to service, and differences on factory definitions.

LightSAML SP Bundle
-------------------

The LightSAML SP Bundle had no issues with Symfony 3, but tests were failing on lowest version - Symfony 2.3, until functional
test configuration ``framework.session.handler_id`` was not fixed from ``null`` to ``session.handler.native_file``.
