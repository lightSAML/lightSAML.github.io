---
    title: How to receive a SAML message
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

Receiving a SAML message from the HTTP request with the SAML HTTP POST or Redirect binding, in LightSAML is done with the Binding set
of classes. The ``BindingFactory`` can detect the binding type for the given HTTP request and instantiate corresponding Binding class,
``HttpPostBinding`` or ``HttpRedirectBinding``, capable of receiving the SAML message (AuthnRequest, Response...).

First you create Symfony's HttpFoundation Request, instantiate ``BindingFactory`` with that request and get the actual binding,
and finally call the binding ``receive()`` method, that will return deserialized SAML document from the HTTP Request.

```php
<?php
$request = \Symfony\Component\HttpFoundation\Request::createFromGlobals();

$bindingFactory = new \LightSaml\Binding\BindingFactory();
$binding = $bindingFactory->getBindingByRequest($request);

$messageContext = new \LightSaml\Context\Profile\MessageContext();
$binding->receive($request, $messageContext);
/** @var \LightSaml\Model\Protocol\Response $response */
$response = $messageContext->getMessage();

print $response->getID();
```

Working code can be found at
[LightSAML examples](https://github.com/lightSAML/lightSAML/blob/20146f9a9c3769d7aabe4c1b4c5c2d5f3ff2e8e9/examples/how_to_receive_saml_message.php).

Receiving of other SAML documents/messages, like Response is done in the same way. Return value of the ``Binding::receive()`` depends
on the actual message being sent, and so far AuthnRequest, Response, LogoutResponse, and LogoutRequest are supported and implemented.
