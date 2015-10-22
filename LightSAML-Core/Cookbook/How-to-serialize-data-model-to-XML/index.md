---
    title: How to serialize data model to XML
    aside: aside-lightsaml.html
    active: cookbook
    github: https://github.com/lightSAML/lightSAML
---

Each LightSAML data model class implements ``SamlElementInterface`` that has ``serialize()`` method

{% highlight php %}
interface SamlElementInterface
{
    public function serialize(\DOMNode $parent, SerializationContext $context);
}
{% endhighlight%}

Serialization of any SAML element can be done with instantiation of a ``SerializationContext`` and
calling ``serialize()`` method of the element with that context. After that, resulting XML document
can be read from the ``SerializationContext``.

{% highlight php %}
<?php
// examples/how_to_serialize_data_model_to_XML.php

require_once __DIR__.'/how_to_make_authn_request.php';

$serializationContext = new \LightSaml\Model\Context\SerializationContext();

$authnRequest->serialize($serializationContext->getDocument(), $serializationContext);

print $serializationContext->getDocument()->saveXML();
{% endhighlight %}

Result will be similar to the XML presented in [How-to-make-AuthnRequest](../How-to-make-AuthnRequest).