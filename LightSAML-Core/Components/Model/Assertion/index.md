---
    title: LightSAML Core Assertion Model
    aside: aside-lightsaml.html
    active: components
    github: https://github.com/lightSAML/lightSAML
---

Assertion holds information about the bearer user. It can be signed or encrypted. It contains ``Issuer``, ``Subject``, ``Conditions``,
``AttributeStatement``, and ``AuthnStatement``.

<img src="SAML-Assertion.png" alt="SAML Assertion" style="max-width: 90%">

[comment]: <> (  [Assertion|+ID;+Version;+IssueInstant]         )
[comment]: <> (  [Assertion]<>->[Issuer]                        )
[comment]: <> (  [Assertion]<>->[Signature]                     )
[comment]: <> (  [Assertion]<>->[Subject]                       )
[comment]: <> (  [Assertion]<>->[Conditions]                    )
[comment]: <> (  [Assertion]<>->[AttributeStatement]            )
[comment]: <> (  [Assertion]<>->[AuthnStatement]                )

