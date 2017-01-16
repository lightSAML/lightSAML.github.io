---
    title: LightSAML SP Bundle Configuration
    aside: aside-sp-bundle.html
    active: config
    github: https://github.com/lightSAML/SpBundle
---

```yaml
# app/config.yml

light_saml_sp:
    username_mapper:
        # default fallback list of attributes for
        # lightsaml_sp.username_mapper.simple service to use to
        # resolve SAML Response to username
        - "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"
        - "http://schemas.xmlsoap.org/claims/EmailAddress"
        - "http://schemas.xmlsoap.org/claims/CommonName"
        - "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"
        - "urn:oid:0.9.2342.19200300.100.1.3"
        - "uid"
        - "urn:oid:1.3.6.1.4.1.5923.1.1.1.6"
        - "@name_id@"
```

## Security configuration

```yaml
security:
    firewalls:
        main:
            light_saml_sp:
                # Symfony's default options
                # provides user from given username
                provider: username_provider_service_id
                success_handler: success_handler_service_id
                failure_handler: failure_handler_service_id

                # LightSAML options

                # provides username from the received SAML Response
                username_mapper: lightsaml_sp.username_mapper.simple

                # called in case provider didn't return a user to create a new user
                user_creator: ~

                # provides token attributes
                attribute_mapper: lightsaml_sp.attribute_mapper.simple

                # use string as username and create unauthenticated token w/out roles
                # in case creator didn't create a user
                force: false
```
