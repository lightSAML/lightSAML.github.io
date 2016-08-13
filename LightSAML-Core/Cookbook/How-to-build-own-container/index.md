---
    title: How to build own container
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

The ``OwnContainerInterface`` is an abstraction layer providing your own credentials and EntityDescriptor.
The LightSAML library comes with built-in bridge for [Pimple](http://pimple.sensiolabs.org/) container.
You need to construct your own credentials and entity descriptor, pass it to an instance of
``OwnContainerProvider`` and finally register it with the Pimple container.

```php
<?php
$container = new \LightSaml\Bridge\Pimple\Container\BuildContainer(new \Pimple\Container());

$ownCredential = new new \LightSaml\Credential\X509Credential(
    \LightSaml\Credential\X509Certificate::fromFile('/path/to/your/saml.crt'),
    \LightSaml\Credential\KeyHelper::createPrivateKey('path/to/your/saml.key', null, true)
);
// it's important you set entity id so credential resolves are able to find it by entity id
$ownCredential->setEntityId('YOUR_ENTITY_ID');


$ownEntityDescriptorProvider = new \LightSaml\Builder\EntityDescriptor\SimpleEntityDescriptorBuilder(
    'YOUR_ENTITY_ID',
    'https://your.domain/assertion-consumer-service.php',
    null,
    $ownCredential->getCertificate()
);

// or somehow build it yourself
$ownEntityDescriptor = new new \LightSaml\Model\Metadata\EntityDescriptor();
// put stuff to it...
$ownEntityDescriptorProvider = new \LightSaml\Provider\EntityDescriptor\FixedEntityDescriptorProvider(
    $ownEntityDescriptor
);


$container->getPimple()->register(
    new \LightSaml\Bridge\Pimple\Container\Factory\OwnContainerProvider(
        $ownEntityDescriptorProvider,
        [$ownCredential]
    )
);
```

Now services depending on the ``BuildContainerInterface`` will be able to retrieve your entity descriptor and
credentials including your private and public keys.
