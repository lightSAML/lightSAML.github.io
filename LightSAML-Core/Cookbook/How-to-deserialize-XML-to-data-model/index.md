---
    title: How to deserialize XML to data model
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

Each LightSAML data model class implements ``SamlElementInterface`` that has ``deserialize()`` method

{% highlight php %}
interface SamlElementInterface
{
    public function deserialize(\DOMElement $node, DeserializationContext $context);
}
{% endhighlight%}

Deserialization of SAML XML message is done with instantiation of ``DeserializationContext``, loading message XML to
it's DOMDocument, instantiating appropriate LightSAML data class that corresponds with data in the XML, and
calling ``deserialize()`` method on it with the document first child and deserialization context.

{% highlight php %}
<?php
// examples/how_to_deserialize_XML_to_data_model.php

$deserializationContext = new \LightSaml\Model\Context\DeserializationContext();

$xml = <<<EOT
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
EOT;

$deserializationContext->getDocument()->loadXML($xml);

$authnRequest = new \LightSaml\Model\Protocol\AuthnRequest();

$authnRequest->deserialize($deserializationContext->getDocument()->firstChild, $deserializationContext);

var_dump($authnRequest);
{% endhighlight %}
