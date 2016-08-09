---
    title: LightSAML Core Model
    aside: aside-lightsaml.html
    active: components
    github: https://github.com/lightSAML/lightSAML
---

LightSAML Core Model is contained in the ``LightSaml\Model`` namespace and defines the PHP classes that correspond to the SAML schema.
Each model class implements ``SamlElementInterface`` as a way of serialization and deserialization. Also, each model class
inherits ``AbstractSamlModel`` that defines common protected helper methods for serialization/deserialization.

Model similarily to the SAML schema specs contains several sub-models:

 * Metadata
 * Protocol
 * Assertion


## Metadata

Metadata classes implements the
[SAML metadata schema](http://www.oasis-open.org/committees/download.php/35391/sstc-saml-metadata-errata-2.0-wd-04-diff.pdf). Root
objects of the SAML metadata model are ``EntitiesDescriptor`` and ``EntityDescriptor``.

![EntitiesDescriptor](http://yuml.me/diagram/scruffy/class/[EntitiesDescriptor]%3C%3E-items*%3E[EntitiesDescriptor],[EntitiesDescriptor]%3C%3E-items*%3E[EntityDescriptor])

``EntitiesDescriptor`` is a collection of ``EntitiesDescriptor``s and ``EntityDescriptor``s.

![EntityDescriptor](http://yuml.me/diagram/scruffy/class/[EntityDescriptor%7C+entityID;+ID;+validUntil],%20[EntityDescriptor]%3C%3E-items*,%20IdpSsoDescriptor],%20[EntityDescriptor]%3C%3E-items*[SpSsoDescriptor],%20[EntityDescriptor]%3C%3E-0..1[Signature],%20[EntityDescriptor]%3C%3E-organizations*[Organization],%20[EntityDescriptor]%3C%3E-contactPersons*[ContactPerson])

``EntityDescriptor`` is actual party descriptor. It identifies the party by ``entityID``. It has it's own ``ID`` and validity period.
It can be signed with a ``Signature``. It has many ``IdpSsoDescriptor``s and ``SpSsoDescriptor``s that describe the party functionalities
as SP and/or IDP. And it has many ``Organization``s and ``ContactPerson``s.

![SSODescriptor](http://yuml.me/diagram/scruffy/class/[SSODescriptor]<>-*>[SingleLogoutService], [SSODescriptor]^-[IdpSsoDescriptor], [SSODescriptor]^-[SpSsoDescriptor], [IdpSsoDescriptor]<>-*>[SingleSignOnService], [SpSsoDescriptor]<>-*>[AssertionConsumerService])

Both ``IdpSsoDescriptor`` and ``SpSsoDescriptor`` inherits ``SSODescriptor`` that has many ``SingleLogoutService``s, while
``IdpSsoDescriptor`` has many ``SingleSignOnService``s, and ``SpSsoDescriptor`` many ``AssertionConsumerService``s.

![EndPoint](http://yuml.me/diagram/scruffy/class/[Endpoint%7C+binding;+location]%5E-[IndexedEndpoint%7C+index],%20[IndexedEndpoint]%5E-[AssertionConsumerService],%20[Endpoint]%5E-[SingleSignOnService],%20[Endpoint]%5E-[SingleLogoutService])

``EndPoint`` defines a party's service, and it can be ``SingleSignOnService``, ``SingleLogoutService``, and ``AssertionConsumerService``
