---
    title: How to make AuthnRequest
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

AuthnRequest is a SAML message that SP sends to IDP in order to initiate authentication. Most important elements
of an AuthnRequest are:

 * issuer
 * id
 * issue instant

Building of an Authn Request can be done like this:

{% highlight php %}
<?php
// examples/how_to_make_authn_request.php

$authnRequest = new \LightSaml\Model\Protocol\AuthnRequest();
$authnRequest
    ->setAssertionConsumerServiceURL('https://my.site/acs')
    ->setProtocolBinding(\LightSaml\SamlConstants::BINDING_SAML2_HTTP_POST)
    ->setID(\LightSaml\Helper::generateID())
    ->setIssueInstant(new \DateTime())
    ->setDestination('https://idp.com/login')
    ->setIssuer(new \LightSaml\Model\Assertion\Issuer('https://my.entity.id'))
;
{% endhighlight %}

Serialization of such AuthnRequest would produce XML similar to one below.

{% highlight xml %}
<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
    ID="_8dcc6985f6d9f385f0bbd4562ef848ef3ae78d87d7"
    Version="2.0"
    IssueInstant="2015-10-10T15:26:20Z"
    Destination="https://idp.com/login"
    AssertionConsumerServiceURL="https://my.site/acs"
    ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
>
    <saml:Issuer>https://my.entity.id</saml:Issuer>
</samlp:AuthnRequest>
{% endhighlight %}

