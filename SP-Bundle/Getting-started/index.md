---
    title: LightSAML SP Bundle Getting Started
    aside: aside-sp-bundle.html
    active: getting-started
---

To start with LightSAML Symfony SP Bundle follow these steps

## Step 1: Download lightsaml/sp-bundle with composer

Add ``lightsaml/sp-bundle`` to composer

{% highlight bash %}
$ composer.phar require lightsaml/sp-bundle
{% endhighlight %}


## Step 2: Load bundles to kernel

{% highlight php %}
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new LightSaml\SymfonyBridgeBundle\LightSamlSymfonyBridgeBundle(),
        new LightSaml\SpBundle\LightSamlSpBundle(),
    );
}
{% endhighlight %}


## Step 3: Configure symfony bridge

Configure LightSAML Symfony Bridge in your config.yml with own entity id, own credentials, and IDP parties. For the getting
started course you can you LightSAML-Core keys and IDP parties LightSAML is already configured with.

{% highlight yaml %}
// app/config.yml

light_saml_symfony_bridge:
    own:
        entity_id: http://localhost/lightsaml/demosp
        credentials:
            -
                certificate: "%kernel.root_dir%/../vendor/lightsaml/lightsaml/web/sp/saml.crt"
                key:         "%kernel.root_dir%/../vendor/lightsaml/lightsaml/web/sp/saml.key"
                password:    ~
    party:
        idp:
            files:
                - "%kernel.root_dir%/../vendor/lightsaml/lightsaml/web/sp/openidp.feide.no.xml"
                - "%kernel.root_dir%/../vendor/lightsaml/lightsaml/web/sp/testshib-providers.xml"
{% endhighlight %}

For advanced configuration check the
[LightSAML Symfony Bridge configuration](/Symfony-Bridge/Configuration)
documentation page.


## Step 4: Import routing resources

Import LightSAML SP Bundle routing resources, and make logout route

{% highlight yaml %}
// app/config/routing.yml
lightsaml_sp:
    resource: "@LightSamlSpBundle/Resources/config/routing.yml"
    prefix: saml

logout:
    path: /logout
{% endhighlight %}


## Step 5: Create User entity class

For the simplicity sake of this getting started course, your User entity can look like this

{% highlight php %}
<?php
// src/AppBundle/Entity/User.php
namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;
use Symfony\Component\Security\Core\User\UserInterface;

/**
 * @ORM\Entity()
 */
class User implements UserInterface, \Serializable
{
    /**
     * @var int
     * @ORM\Id
     * @ORM\Column(type="integer")
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    protected $id;

    /**
     * @var string
     * @ORM\Column(type="string")
     */
    protected $username;

    /**
     * @var array
     * @ORM\Column(type="json_array")
     */
    protected $roles;

    /**
     * @return int
     */
    public function getId()
    {
        return $this->id;
    }

    /**
     * @return array
     */
    public function getRoles()
    {
        return $this->roles;
    }

    /**
     * @param array $roles
     *
     * @return User
     */
    public function setRoles($roles)
    {
        $this->roles = $roles;

        return $this;
    }

    /**
     * @return string
     */
    public function getUsername()
    {
        return $this->username;
    }

    /**
     * @param string $username
     *
     * @return User
     */
    public function setUsername($username)
    {
        $this->username = $username;

        return $this;
    }

    // -------------------------------------------

    /**
     * @return string
     */
    public function getPassword()
    {
        return '';
    }

    /**
     * @return string|null
     */
    public function getSalt()
    {
        return '';
    }

    public function eraseCredentials()
    {
    }

    /**
     * @return string
     */
    public function serialize()
    {
        return serialize([$this->id, $this->username, $this->roles]);
    }

    /**
     * @return void
     */
    public function unserialize($serialized)
    {
        list($this->id, $this->username, $this->roles) = unserialize($serialized);
    }
}

{% endhighlight %}


## Step 6: Update database schema

Update your database with the ``User`` entity schema.

If you are using doctrine migrations, make a diff and migrate

{% highlight bash %}
$ app/console doc:mig:dif
$ app/console doc:mig:mig -n
{% endhighlight %}

In case you're not using migrations, just force the schema update

{% highlight bash %}
$ app/console doctrine:schema:update --force
{% endhighlight %}


## Step 7: Define Symfony security user provider

Add entity user provider to your security configuration so Symfony security can read users from the entity you created in Step 5

{% highlight yaml %}
# app/config/security.yml
security:
    providers:
        db_provider:
            entity:
                class: AppBundle:User
                property: username
{% endhighlight %}


## Step 8: Implement User Creator service

In case user provider defined in previous step can not retrieve a user, LightSAML SP Bundle security authentication provider,
if configured so which will be done in next step, will call user creator to create a new user for the given SAML Response.

{% highlight php %}
<?php
// src/AppBundle/Security/User/UserCreator.php
namespace AppBundle\Security\User;

use AppBundle\Entity\User;
use Doctrine\Common\Persistence\ObjectManager;
use LightSaml\Model\Protocol\Response;
use LightSaml\SpBundle\Security\User\UserCreatorInterface;
use LightSaml\SpBundle\Security\User\UsernameMapperInterface;
use Symfony\Component\Security\Core\User\UserInterface;

class UserCreator implements UserCreatorInterface
{
    /** @var ObjectManager */
    private $objectManager;

    /** @var UsernameMapperInterface */
    private $usernameMapper;

    /**
     * @param ObjectManager           $objectManager
     * @param UsernameMapperInterface $usernameMapper
     */
    public function __construct($objectManager, $usernameMapper)
    {
        $this->objectManager = $objectManager;
        $this->usernameMapper = $usernameMapper;
    }

    /**
     * @param Response $response
     *
     * @return UserInterface|null
     */
    public function createUser(Response $response)
    {
        $username = $this->usernameMapper->getUsername($response);

        $user = new User();
        $user
            ->setUsername($username)
            ->setRoles(['ROLE_USER'])
        ;

        $this->objectManager->persist($user);
        $this->objectManager->flush();

        return $user;
    }
}
{% endhighlight %}

Register it as a service

{% highlight yaml %}
# app/config/services.yml
services:
    user_creator:
        class: AppBundle\Security\User\UserCreator
        arguments:
            - "@=service('doctrine').getManager()"
            - "@lightsaml_sp.username_mapper.simple"
{% endhighlight %}


## Step 9: Configure security

Configure Symfony security firewall to use LightSAML SP Bundle security listener with previously defined user provider and creator.

{% highlight yaml %}
# app/config/security.yml
security:
    firewalls:
        main:
            light_saml_sp:
                provider: db_provider
                user_creator: user_creator
                login_path: /saml/login
                check_path: /saml/login_check
            logout:
                path: /logout
            anonymous: ~

    access_control:
        - { path: ^/secure, roles: ROLE_USER }

{% endhighlight %}


## Step 10: Create homepage and secure pages

Create two pages, one public, other secure, and their corresponding templates, so we can test the security.

{% highlight php %}
<?php
// src/AppBundle/Controller/DefaultController.php
namespace AppBundle\Controller;

use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;
use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpFoundation\Request;

class DefaultController extends Controller
{
    /**
     * @Route("/", name="homepage")
     * @Template("default/index.html.twig")
     */
    public function indexAction(Request $request)
    {
        return [];
    }

    /**
     * @Route("/secure", name="secure")
     * @Template("default/secure.html.twig")
     */
    public function indexAction()
    {
        return [];
    }
}
{% endhighlight %}


## Step 11: Open login page

Go to the ``/saml/login`` route. Since in Step 3 we provided two IDP parties, you'll be redirected to a discovery page ``/saml/discovery``
to choose one of them.  When you select IDP, you will be redirect to it's login location with AuthnRequest, and when you authenticate
on that IDP, you'll be redirected back to ``/saml/login_check`` route with Assertion.


## Next Reads

 * foo
 * bar

