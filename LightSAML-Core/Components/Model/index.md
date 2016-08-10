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

 * [Metadata](#metadata)
 * [Protocol](#protocol)
 * [Assertion](#assertion)

## Metadata

Metadata classes implements the
[SAML metadata schema](http://www.oasis-open.org/committees/download.php/35391/sstc-saml-metadata-errata-2.0-wd-04-diff.pdf). Root
objects of the SAML metadata model are ``EntitiesDescriptor`` and ``EntityDescriptor``.

[SAML Metadata details](Metadata/){:class="more"}

## Protocol

Protocol classes implements the SAML Protocols defined in
[SAML 2.0 core](http://www.oasis-open.org/committees/download.php/35711/sstc-saml-core-errata-2.0-wd-06-diff.pdf). Root objects
of the SAML protocol are: AuthnRequest, Response, LogoutRequest, and LogoutResponse.

[SAML Protocol details](Protocol/){:class="more"}


## Assertion

Assertion holds information about the bearer user. It can be signed or encrypted. It contains ``Issuer``, ``Subject``, ``Conditions``,
``AttributeStatement``, and ``AuthnStatement``.

[SAML Assertion details](Assertion/){:class="more"}
