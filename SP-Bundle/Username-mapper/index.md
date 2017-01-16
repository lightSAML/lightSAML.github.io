---
    title: LightSAML SP Bundle Username mapper
    aside: aside-sp-bundle.html
    active: username_mapper
    github: https://github.com/lightSAML/SpBundle
---


LightSAML authentication provider in order to [resolve user](../User-resolution) and eventually user Symfony's ``UserProviderInterface``
from the received SAML Response and it's Assertions, first have to determine the username to call user provider with. For that purpose
it uses the ``UsernameMapperInterface``.

```php
interface UsernameMapperInterface
{
    /**
     * @param Response $response
     *
     * @return string|null
     */
    public function getUsername(Response $response);
}
```

It takes the received SAML Response and returns the username.

By default LightSAML SP Bundle uses ``SimpleUsernameMapper`` which takes an array of attribute names as constructor argument, and when
called to get the username, it iterates trough that list of attributes and returns the value of the first attribute it finds in the
SAML Response. In addition it defines a special attribute name ``"@name_id@"`` which indicates assertion's NameID.

For example if received Assertion has following attributes

```xml
<AttributeStatement>
    <Attribute Name="http://schemas.xmlsoap.org/claims/CommonName">
        <AttributeValue>john</AttributeValue>
    </Attribute>
    <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress">
        <AttributeValue>john@example.com</AttributeValue>
    </Attribute>
</AttributeStatement>
```

and you want username to be email or eventually user principal name in case email not provided, then you will instantiate
and user the ``SimpleUsernameMapper`` this way

```php
<?php
$samlResponse = getSamlResponse(); // received SAML Response
$usernameMapper = new SimpleUsernameMapper([
    'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress',
    'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn',
]);

$username = $usernameMapper->getUsername($samlResponse); // john@example.com
```

## Custom username mapper

In case this kind simple cascading resolution of the username from received SAML Response does not fit your needs, you can implement
your own username mapper

```php
<?php

/**
 * @Service(id="my_own_username_mapper")
 */
class MyOwnUsernameMapper implements UsernameMapperInterface
{
    public function getUsername(Response $response)
    {
        // complex algorithm for determine username from received SAML response
        return $username;
    }
}
```

and set it to the firewall security config ``app/config/security.yml``

```yaml
security:
    firewalls:
        main:
            light_saml_sp:
                # ...
                username_mapper: my_own_username_mapper
```

That way, you will replace default simple username mapper with your own implementation.
