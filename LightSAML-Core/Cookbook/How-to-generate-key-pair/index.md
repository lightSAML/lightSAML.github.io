---
    title: How to generate a key pair
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

In order to use any of the SAML security features like signatures and encryption, you would first need a key pair. In consists of
a public part - the certificate, and a private key. Private key is used to sign SAML messages, while public key is used to encrypt
and message so only you can decrypt it, and to verify your signatures. Certificate is published with your SAML metadata and is freely
distributed to your relying parties. Private key, just as it's name says, should remain private and for your eyes only. Due to security
issues, certificates expire after some time, and you have to renew them in order to keep SAML signing and encryption working.

You can generate a key pair with [OpenSSL](https://www.openssl.org/). It's a complex suit with several bundled tools, but the easiest
way is

```
$ openssl req -new -x509 -days 365 -nodes -sha256 -out saml.crt -keyout saml.pem
```

That command line will produce two files ``saml.crt`` - the certificate with a public key, and ``saml.pem`` - your private key. You need
to provide those two files to the LightSAML in order to use SAML security features.

**Note**: The ``-sha256`` switch tells OpenSSL to generate a certificate using SHA-256 digest algorithm. By default, if you omit that
switch, you'll get a SHA-1 digest which is considered week these days, and you should avoid it.


## Using key pair with LightSAML

You can load a certificate file using static method ``fromFile`` on class ``X509Certificate``:

```php
<?php
$certificate = X509Certificate::fromFile('/path/to/saml.crt');
```

You can load your private key using ``KeyHelper`` class

```php
<?php
$privateKey = KeyHelper::createPrivateKey('/path/to/saml.pem', '', true, XMLSecurityKey::RSA_SHA256);
```

You can sign a SAML message by setting an instance of ``SignatureWriter`` to it's signature property and serializing it afterwards.

```php
<?php
$message->setSignature(SignatureWriter::createByKeyAndCertificate($certificate, $privateKey));
$context = new SerializationContext();
$message->serialize($context->getDocument(), $context);
```

For details about signing look at [How to sign a SAML message](../How-to-sign-SAML-message) cookbook article.


## Inspecting generated certificate

Once generated certificate can be inspected with following command line

```
$ openssl x509 -in saml.crt -text -noout
```

Important things to look for are following

Digest algorithm used

```
Signature Algorithm: sha256WithRSAEncryption
```

Issuer

```
Issuer: C=AU, ST=Some-State, O=Internet Widgits Pty Ltd, CN=common name
```

And validity dates

```
Validity
    Not Before: Aug  9 07:04:20 2016 GMT
    Not After : Aug  9 07:04:20 2017 GMT
```

