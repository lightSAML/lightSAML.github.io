---
    title: LightSAML Core Components
    aside: aside-lightsaml.html
    active: components
    github: https://github.com/lightSAML/lightSAML
---

## Model

LightSAML Core Model is contained in the ``LightSaml\Model`` namespace and defines the PHP classes that correspond to the SAML schema.
Each model class implements ``SamlElementInterface`` as a way of serialization and deserialization. Also, each model class
inherits ``AbstractSamlModel`` that defines common protected helper methods for serialization/deserialization.

[more about LightSAML Model](Model/){:class="more"}

## Validator

LightSAML Core Validator is a set of classes that validates if syntax and data model values are valid according to the SAML specifications.
Validation is lightweight and should be performed before any other heavier processing of the message, like for example signature
verification or description.

Validators for the following data models are implemented:

 * Assertion
 * NameId
 * Signature
 * Statement
 * Subject

## Binding

LightSAML supports HTTP-Redirect and HTTP-POST SAML Bindings. They are implemented in the ``LightSAML\Binding`` namespace. You can use
``BindingFactory`` to detect bending of the http request, and to instantiate a concrete binding to send and receive SAML messages.

## Credential

The credential part of LightSAML defines a data model used for storing SAML party credentials used for signing and encryption.
Main class is the X509Credential that is an aggregate of a private key and X509 certificate.

![Credential](http://yuml.me/diagram/scruffy/class/[%3C%3CCredentialInterface%3E%3E]%5E-.-[AbstractCredential%7C%7C+entityId;+usage;+publicKey;+privateKey],%20[AbstractCredential]%5E-[X509Credential],%20[%3C%3CX509CredentialInterface%3E%3E]%5E-.-[X509Credential],%20[AbstractCredential]%3C%3E-0..1%3E[XMLSecurityKey],%20[AbstractCredential]%3C%3E-0..1%3E[XMLSecurityKey],%20[X509Credential]%3C%3E-0..1%3E[X509Certificate],%20[XMLSecurityKey]-.-[private%20and%20public%20keys%7Bbg:wheat%7D])


## Context

Context is an argument for all profile actions. A context has zero or one parent context, and it has many sub-contexts. Context itself
is a hierarchical structure of data, and depending of its class, it contains different data and values. For example, there
are ``SerializationContext`` and ``DeserializationContext``, and as it's names say, they are
used during serialization and deserialization of SAML messages. Then, there is ``MessageContext`` and holds a SAML message, and profile
actions during execution get the actual message trough it. There's also ``ProfileContext`` that's the root starting context for the
profile action, and during execution of the profile action tree, other contexts are attached to it.


## Action

Actions are operations that do something on the given context. Different actions accept different contexts. A concrete SAML Profile
is composed as an action tree and is composed of many actions. Profile and action builders provide profile actions.

## Store

There are several different stores in LightSAML and they should persist different data. Most of the LightSAML Core implementations are
abstract or static array stores. Some stores use session or filesystem to read/store data and they supported by LightSAML
out of the box, while other stores should be cross session persistent and it's up to the library user to implement concrete
store that can usefully persist data.

## Provider

Providers are various abstractions and extension points to the LightSAML Core library. For example there is
``AttributeNameProviderInterface`` that is used to provide SAML attributes for a particular user during building of the SAML Response.

## Resolver

Resolvers are components that for a given criteria return particular data that matches that given query. They are mostly used by actions
as an abstraction layer above stores and providers.

## Build Container

LightSAML Core consists of many smaller components/classes that individually do single task. Build container is a service registry
abstraction that can provide all those small services.

 * ``BuildContainerInterface`` - a root service container used to reach other containers
 * ``OwnContainerInterface`` - a container for your own Credentials and Entity descriptor
 * ``SystemContainerInterface`` - a container acting as an abstraction layer to system, providing HTTP Request, Session, Time provider, Event dispatcher
 * ``PartyContainerInterface`` - a container of the relaying parties - their Entity descriptors and Trust options
 * ``StoreContainerInterface`` - a container of stores
 * ``ProviderContainerInterface`` - a container of provider services
 * ``CredentialContainerInterface`` - a container of credentials, both your own and of relaying parties
 * ``ServiceContainerInterface`` - a container of various logical services like validators and resolvers

## Builder

Builders are factories that construct ProfileContext and profile action tree for the specific SAML profile. LightSAML implements Metadata
and Web Browser Single Sign On SSO SAML profiles
