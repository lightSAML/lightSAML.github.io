---
title:  "Symfony 3 support for LightSAML"
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
in order to support Symfony 3, but it suffered few modifications to support Symfony 2.3, since it was initially written in version 2.7.
Those were issues with injecting http request to service, and differences on factory definitions.

In Symfony version 2.3 request is a
[synchronized service](http://symfony.com/blog/new-in-symfony-2-7-dependency-injection-improvements#deprecated-synchronized-services)
so simple retrieval from container does the job, while in Symfony 2.7 synchronized services are deprecated and
``request_stack`` has to be used. Solution I found to work, was to first check the container for the existence of ``request_stack``, and
if it's not defined to retrieve the ``request`` synchronized service.


{% highlight php %}
<?php

class SystemContainer extends AbstractContainer implements SystemContainerInterface
{
    /**
     * @return Request
     */
    public function getRequest()
    {
        if ($this->container->has('request_stack')) {
            return $this->container->get('request_stack')->getCurrentRequest();
        }

        return $this->container->get('request');
    }
}
{% endhighlight%}

Factory definition is handled diferently in Symfony 2.3 and 3.0, since version 2.6 the new ``setFactory()`` method was
[introduced](http://symfony.com/doc/2.6/components/dependency_injection/factories.html), while in version 3.0 old ``setFactoryClass()``
and ``setFactoryMethod()`` methods are deprecated. Thus in the bundle extension, you first have to check if DPI ``Defintion`` class
has new ``setFactory()`` method, and as a fallback use old version 2.3 methods.

{% highlight yaml %}
services:
    lightsaml.own.entity_descriptor_provider:
        class: LightSaml\Builder\EntityDescriptor\SimpleEntityDescriptorBuilder
        # factory set in extension, differently based on symfony version
{% endhighlight%}

{% highlight php %}
<?php
    // LightSamlSymfonyBridgeExtension
    // ...
    private function configureOwnEntityDescriptor(ContainerBuilder $container, array $config) {
        // ...
        $definition = $container->getDefinition('lightsaml.own.entity_descriptor_provider');
        if (method_exists($definition, 'setFactory')) {
            $definition->setFactory([
                'LightSaml\SymfonyBridgeBundle\Factory\OwnEntityDescriptorProviderFactory',
                'build'
            ]);
        } else {
            $definition->setFactoryClass(
                'LightSaml\SymfonyBridgeBundle\Factory\OwnEntityDescriptorProviderFactory'
            );
            $definition->setFactoryMethod('build');
        }
    }
    // ...
{% endhighlight%}

LightSAML SP Bundle
-------------------

The LightSAML SP Bundle had no issues with Symfony 3, but tests were failing on lowest version - Symfony 2.3, until functional
test configuration ``framework.session.handler_id`` was not fixed from ``null`` to ``session.handler.native_file``.

