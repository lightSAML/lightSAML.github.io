---
    title: How to send a SAML message
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

SAML messages are transmitted using one of the bindings SAML defines. LightSAML implements *HTTP-Redirect* and *HTTP-POST bindings*.
Specific binding defines how serialized SAML message XML will be encoded and transferred. Both bindings base64 encode
the SAML XML. *HTTP-Redirect* binding uses the HTTP GET method and puts base64 encoded SAML message XML in the query parameter.
The *HTTP-POST* binding sends a message trough a HTML form with POST method.

LightSAML implements an ``AbstractBinding`` class that encapsulates all bindings behavior - sending and receiving messages. You
should use ``BindingFactory`` to obtain specific binding instance, passing the binding type constant to it's ``create()`` method.

Binding instance requires a ``MessageContext`` instance as an argument, and that MessageContext should have the message property set
before calling the ``send()`` method. Both *HTTP-POST* and *HTTP-Redirect* bindings ``send()`` methods return an instance of
``Symfony\Component\HttpFoundation\Response``.


## Redirect binding

Redirect binding ``send()`` method returns an instance of ``Symfony\Component\HttpFoundation\RedirectResponse`` and it's ``targetUrl``
property is the location user should be redirected to.

```php
<?php
$authnRequest = new \LightSaml\Model\Protocol\AuthnRequest();
$authnRequest->setDestination('http://destination.com');

$bindingFactory = new \LightSaml\Binding\BindingFactory();
$redirectBinding = $bindingFactory->create(\LightSaml\SamlConstants::BINDING_SAML2_HTTP_REDIRECT);

$messageContext = new \LightSaml\Context\Profile\MessageContext();
$messageContext->setMessage($authnRequest);

/** @var \Symfony\Component\HttpFoundation\RedirectResponse $httpResponse */
$httpResponse = $redirectBinding->send($messageContext);
print $httpResponse->getTargetUrl();
```

The target redirect url for the example above would be similar to:

```
http://destination.com?SAMLRequest=BASE64.ENCODED.MESSAGE%3D%3D
```

## Post binding

The Post binding ``send()`` method returns an instance of ``Symfony\Component\HttpFoundation\Response`` with it's content being
and HTML form that will automatically POST submit to the destination with encoded SAML message.

```php
<?php
$authnRequest = new \LightSaml\Model\Protocol\AuthnRequest();
$authnRequest->setDestination('http://destination.com');

$bindingFactory = new \LightSaml\Binding\BindingFactory();
$postBinding = $bindingFactory->create(\LightSaml\SamlConstants::BINDING_SAML2_HTTP_POST);

$messageContext = new \LightSaml\Context\Profile\MessageContext();
$messageContext->setMessage($authnRequest);

/** @var \Symfony\Component\HttpFoundation\Response $httpResponse */
$httpResponse = $postBinding->send($messageContext);
print $httpResponse->getContent();
```

Generated HTML for the example above would look similar to

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
<head>
    <meta http-equiv="content-type" content="text/html; charset=utf-8" />
    <title>POST data</title>
</head>
<body onload="document.getElementsByTagName('input')[0].click();">

    <noscript>
        <p><strong>Note:</strong> Since your browser does not support JavaScript, you must
            press the button below once to proceed.</p>
    </noscript>

    <form method="post" action="http://destination.com">
        <input type="submit" style="display:none;" />

        <input type="hidden" name="SAMLRequest" value="BASE64.ENCODED.MESSAGE=" />

        <noscript>
            <input type="submit" value="Submit" />
        </noscript>

    </form>
</body>
</html>
```


Working code can be found at
[LightSAML examples](https://github.com/lightSAML/lightSAML/blob/master/examples/how_to_send_SAML_message.php).
