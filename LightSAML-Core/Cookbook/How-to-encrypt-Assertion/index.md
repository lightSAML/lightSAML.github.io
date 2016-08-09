---
    title: How to encrypt Assertion
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

LightSAML supports SAML Assertion encryption. First you need normally to create the ``Assertion`` with all the data you need. Then
create a new instance of ``EncryptedAssertionWriter`` and call it's ``encrypt`` method with the created assertion object and
certificate of the recipient. Finally assign that encryption writer to the SAML Response.

```php
<?php
$assertion = new Assertion();
// fill it up with data...
$certificate = X509Certificate::fromFile('/path/to/saml.crt');
$encryptedAssertion = new EncryptedAssertionWriter();
$encryptedAssertion->encrypt($assertion, KeyHelper::createPublicKey($certificate));
$response = new Response();
$response->addEncryptedAssertion($encryptedAssertion);
$context = new SerializationContext();
$response->serialize($context->getDocument(), $context);
```

It will produce an XML similar to
[encryptedAssertion01.xml](https://github.com/lightSAML/lightSAML/blob/master/resources/sample/Response/encryptedAssertion01.xml)

**Note**: The SAML protocol also specifies that Assertion should be signed before encryption, while during receiving the Assertion
procedure is reversed - you first decrypt it, and then verify it's signature.

Full SAML SSO IDP profile is being developed and a separate [LightSAML-IDP library](https://github.com/lightSAML/lightSAML-IDP).

