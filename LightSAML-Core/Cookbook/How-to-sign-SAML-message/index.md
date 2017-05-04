---
    title: How to sign SAML message
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

Signing all SAML objects (``AuthnRequest``, ``Response``, ``Assertion``, ``LogoutRequest``, and ``LogoutResponse``) is all done in the
same way - by setting an instance of the ``SignatureWriter`` to object's signature property - by the **``setSignature()``** method.
Since signing all SAML objects is the same, only the example of signing one of them is given below - signing of the ``AuthnRequest``.

In order to instantiate ``SignatureWrite`` and enable it for signing, you have to provide to it's constructor (or set it later on, but
before the serialization) your **certificate** and your **private key**.

```php
<?php
$authnRequest = new \LightSaml\Model\Protocol\AuthnRequest();
$authnRequest
    ->setAssertionConsumerServiceURL('https://my.site/acs')
    ->setProtocolBinding(\LightSaml\SamlConstants::BINDING_SAML2_HTTP_POST)
    ->setID(\LightSaml\Helper::generateID())
    ->setIssueInstant(new \DateTime())
    ->setDestination('https://idp.com/login')
    ->setIssuer(new \LightSaml\Model\Assertion\Issuer('https://my.entity.id'))
;

$certificate = \LightSaml\Credential\X509Certificate::fromFile('certificate.crt');
$privateKey = \LightSaml\Credential\KeyHelper::createPrivateKey('private.key', '', true);

$authnRequest->setSignature(new \LightSaml\Model\XmlDSig\SignatureWriter($certificate, $privateKey));

$serializationContext = new \LightSaml\Model\Context\SerializationContext();
$authnRequest->serialize($serializationContext->getDocument(), $serializationContext);
```

Serialization of such AuthnRequest would produce formatted XML similar to one below.

```xml
<AuthnRequest xmlns="urn:oasis:names:tc:SAML:2.0:protocol"
    ID="_8d3d46271c2e234f6b0d79f6d2716c707746abf9ca"
    Version="2.0"
    IssueInstant="2016-07-27T13:33:50Z"
    Destination="https://idp.com/login"
    ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
    AssertionConsumerServiceURL="https://my.site/acs"
>
    <saml:Issuer xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion">https://my.entity.id</saml:Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>
            <ds:Reference URI="#_8d3d46271c2e234f6b0d79f6d2716c707746abf9ca">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>
                <ds:DigestValue>Ez74FQ0Nqwre+mL8/Zsceekeh/s=</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>SIGNATURE.BYTES.HERE==</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>CERTIFICATE.BYTES.HERE=</ds:X509Certificate>
            </ds:X509Data>
        </ds:KeyInfo>
    </ds:Signature>
</AuthnRequest>
```
