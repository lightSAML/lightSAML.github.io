---
    title: LightSAML Symfony Bridge Bundle Configuration
    github: https://github.com/lightSAML/SymfonyBridgeBundle
    aside: aside-symfony-bridge.html
---

```yaml
# app/config.yml

light_saml_symfony_bridge:
    own:
        entity_id: https://your.entity.id # required
        entity_descriptor_provider:
            id: own_entity_descriptor_provider_service_id
            filename: /path/to/own-entity-descriptor.xml
        credentials:
            -
                certificate: /path/to/certificate.crt
                key:         /path/to/private-key.pem
                password:    private-key-secret
    system:
        event_dispatcher: ~
        logger: ~
    store:
        request: ~
        id_state: ~
        sso_state: ~
    party:
        idp:
            files:
                - /path/to/idp-entity-descriptor.xml
```


## Tags

### lightsaml.own_credential_store

Service which implements ``CredentialStoreInterface`` providing own credentials when asked for own entity id


### lightsaml.trust_options_store

Service which implements ``TrustOptionsStoreInterface`` providing ``TrustOptions`` for given entity id


### lightsaml.idp_entity_store

Service which implements ``EntityDescriptorStoreInterface`` providing IDP entity descriptors.


### lightsaml.credential

Service which implements ``CredentialInterface`` that will be used as extra credential in addition to all other credentials
available from party entity descriptors.

