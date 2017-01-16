---
    title: LightSAML SP Bundle Attribute mapper
    aside: aside-sp-bundle.html
    active: attribute_mapper
    github: https://github.com/lightSAML/SpBundle
---


The LightSAML authentication provider, once the user is retrieved from provider or created by creator, creates the security token
with attributes from the attribute mapper specified in the [security configuration](../Configuration#security-configuration).

```php
<?php
interface AttributeMapperInterface
{
    /**
     * @param SamlSpResponseToken $token
     *
     * @return array
     */
    public function getAttributes(SamlSpResponseToken $token);
}
```

Default attribute mapper is ``SimpleAttributeMapper`` that collects all attributes from all attribute statements of all
assertions.

## Custom attribute mapper

To precisely control which attributes you will store in the security token, you can implement your own attribute mapper

```php
<?php

/**
 * @Service(id="my_own_attribute_mapper")
 */
class MyOwnAttributeMapper implements AttributeMapperInterface
{
    public function getAttributes(SamlSpResponseToken $token)
    {
        return [
            'name_id' => $token->getResponse()->getFirstAssertion()->getSubject()->getNameID()->getValue(),
            'email' => $token->getResponse()->getFirstAssertion()->getFirstAttributeStatement()
                ->getFirstAttributeByName(ClaimTypes::EMAIL_ADDRESS)->getFirstAttributeValue(),
        ];
    }
}
```

And specify it to be used in the security configuration.

```yaml
# app/config/security.yml
security:
    firewalls:
        main:
            light_saml_sp:
                attribute_mapper: my_own_attribute_mapper
```
