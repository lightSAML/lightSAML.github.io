---
    title: LightSAML SP Bundle User resolution
    aside: aside-sp-bundle.html
    active: user-resolution
    github: https://github.com/lightSAML/SpBundle
---

Once SAML Response is received LightSAML SP-Bundle authentication provider has to resolve it to a corresponding User.
That resolution is done through several steps, that this document will explain in details.

![User resolution](/images/sp-bundle/user-resolution-01-resolution.png)


## Username mapper

First step in resolving the user from SAML Response is to check if it's an existing user. In order to do that,
we need the **username**.

![Username mapper](/images/sp-bundle/user-resolution-02-username.png)

Username mapper responsibility is to provide username for a given SAML Response.

{% highlight php %}
<?php
interface UsernameMapperInterface
{
    /**
     * @param \LightSaml\Model\Protocol\Response $response
     *
     * @return string|null
     */
    public function getUsername(\LightSaml\Model\Protocol\Response $response);
}
{% endhighlight %}

LightSAML implements ``SimpleUsernameMapper`` that goes trough all response assertions and their attributes, and
returns first attribute value if matched with one of the attribute names from the provided cascading fallback list.

The ``light_saml_sp`` security listener has a configuration ``username_mapper`` that you can you to specify the
service name of your username mapper. It defaults to ``lightsaml_sp.username_mapper.simple`` service,
which is ``SimpleUsernameMapper`` instance with the cascading fallback list of attributes from the bundle
configuration ``light_saml_sp.username_mapper``.

Configuration of the ``light_saml_sp`` security listener is shown in
[Getting started - Step 9: Configure security](../Getting-started/#step-9-configure-security),
and default cascading fallback list of attributes for ``SimpleUsernameMapper`` is documented on
[Configuration](../Configuration) page.

If default behavior does not fit your needs, you can set your own value for ``light_saml_sp.username_mapper``
bundle configuration in ``app/config/config.yml``, and keep using ``SimpleUsernameMapper``, or you could write
your own implementation of the ``UsernameMapperInterface`` and set it's service name to
the configuration of ``light_saml_sp`` security listener in ``app/config/security.yml``.


## Symfony User Provider

Once we resolved the username from the SAML Response, we can call Symfony's User Provider and try to load
such user if it exists. If an instance of ``Symfony\Component\Security\Core\User\UserInterface`` is returned,
the user resolution is finished right away, based just on the SAML Response to username mapping, provided by
username mapper.

![User provider](/images/sp-bundle/user-resolution-03-provider.png)

The ``provider`` is common Symfony options for each
[security listener configuration](http://symfony.com/doc/current/reference/configuration/security.html).


## User creator

If user provider didn't return User, it has to throw ``UsernameNotFoundException`` and in that case
user creator is called with expectation to create a new User based on the given SAML Response, if possible.

![User creator](/images/sp-bundle/user-resolution-04-creator.png)

You can find the example user creator in
[Getting started - Step 8: Implement User Creator service](../Getting-started/#step-8-implement-user-creator-service).

