---
    title: How to decrypt Assertion
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

To decrypt a SAML Assertion from the Response with encrypted Assertion you would need your key pair the Assertion
was encrypted for. The sender encrypted the SAML Assertion having your public key which you gave to then
trough certificate in your metadata XML.

First you deserialize the XML into the Response data model object. Then you create a Credential with your
key pair. Finally you decrypt the SAML Assertion with that credential and get the decrypted Assertion.

Working example can be found in
[LightSAML-Core examples](https://github.com/lightSAML/lightSAML/blob/f125b2a70cb9c076f4ca86c156cbac297da43fe4/examples/how_to_decrypt_assertion.php).

```php
<?php
$xml = '<samlp:Response><saml:EncryptedAssertion>...</saml:EncryptedAssertion></samlp:Response>';

// deserialize XML into a Response data model object
$deserializationContext = new \LightSaml\Model\Context\DeserializationContext();
$deserializationContext->getDocument()->loadXML($xml);
$response = new \LightSaml\Model\Protocol\Response();
$response->deserialize(
    $deserializationContext->getDocument()->firstChild,
    $deserializationContext
);

// load you key par credential
$credential = new \LightSaml\Credential\X509Credential(
    \LightSaml\Credential\X509Certificate::fromFile('my.crt'),
    \LightSaml\Credential\KeyHelper::createPrivateKey('my.key', '', true)
);

// decrypt the Assertion with your credential
$decryptDeserializeContext = new \LightSaml\Model\Context\DeserializationContext();
/** @var \LightSaml\Model\Assertion\EncryptedAssertionReader $reader */
$reader = $response->getFirstEncryptedAssertion();
$assertion = $reader->decryptMultiAssertion([$credential], $decryptDeserializeContext);

// use decrypted assertion
foreach ($assertion->getFirstAttributeStatement()->getAllAttributes() as $attribute) {
    print sprintf("%s: %s\n", $attribute->getName(), $attribute->getFirstAttributeValue());
}
```
