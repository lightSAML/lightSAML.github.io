---
    title: LightSAML SP Bundle Getting Started
    aside: aside-sp-bundle.html
    active: getting-started
    github: https://github.com/lightSAML/SpBundle
---

To start with LightSAML Symfony SP Bundle follow these steps

## Step 1: Download lightsaml/sp-bundle with composer

Add ``lightsaml/sp-bundle`` to composer

```bash
$ composer.phar require lightsaml/sp-bundle
```


## Step 2: Load bundles to kernel

```php
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
```


## Step 3: Import routing resources

Import LightSAML SP Bundle routing resources, and make logout route

```yaml
// app/config/routing.yml
lightsaml_sp:
    resource: "@LightSamlSpBundle/Resources/config/routing.yml"
    prefix: saml

logout:
    path: /logout
```


## Step 4: Create User entity class

For the simplicity sake of this getting started course, your User entity can look like this

```php
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
```


## Step 5: Create ID entry entity

Though not explicitly required by the bundle, it is highly recommended that you track received message IDs in order
to protect against message repetition. For those purposes, you need an entity to persist those IDs.

```php
<?php
// src/AppBundle/Entity/IdEntry.php
namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Table(name="id_entries")
 * @ORM\Entity()
 */
class IdEntry
{
    /**
     * @var string
     * @ORM\Id
     * @ORM\GeneratedValue(strategy="NONE")
     * @ORM\Column(type="string", length=255, nullable=false)
     */
    protected $entityId;

    /**
     * @var string
     * @ORM\Id
     * @ORM\GeneratedValue(strategy="NONE")
     * @ORM\Column(type="string", length=255, nullable=false)
     */
    protected $id;

    /**
     * @var int
     * @ORM\Column(type="integer", nullable=false)
     */
    protected $expiryTimestamp;

    /**
     * @return string
     */
    public function getEntityId()
    {
        return $this->entityId;
    }

    /**
     * @param string $entityId
     *
     * @return IdEntry
     */
    public function setEntityId($entityId)
    {
        $this->entityId = $entityId;

        return $this;
    }

    /**
     * @return \DateTime
     */
    public function getExpiryTime()
    {
        $dt = new \DateTime();
        $dt->setTimestamp($this->expiryTimestamp);

        return $dt;
    }

    /**
     * @param \DateTime $expiryTime
     *
     * @return IdEntry
     */
    public function setExpiryTime(\DateTime $expiryTime)
    {
        $this->expiryTimestamp = $expiryTime->getTimestamp();

        return $this;
    }

    /**
     * @return string
     */
    public function getId()
    {
        return $this->id;
    }

    /**
     * @param string $id
     *
     * @return IdEntry
     */
    public function setId($id)
    {
        $this->id = $id;

        return $this;
    }
}
```


## Step 6: Create ID Store service

Implement LightSAML ``IdStoreInterface``

```php
<?php
// src/AppBundle/Store/IdStore.php
namespace AppBundle\Store;

use AppBundle\Entity\IdEntry;
use Doctrine\Common\Persistence\ObjectManager;
use LightSaml\Provider\TimeProvider\TimeProviderInterface;
use LightSaml\Store\Id\IdStoreInterface;

class IdStore implements IdStoreInterface
{
    /** @var ObjectManager */
    private $manager;

    /** @var  TimeProviderInterface */
    private $timeProvider;

    /**
     * @param ObjectManager         $manager
     * @param TimeProviderInterface $timeProvider
     */
    public function __construct(ObjectManager $manager, TimeProviderInterface $timeProvider)
    {
        $this->manager = $manager;
        $this->timeProvider = $timeProvider;
    }

    /**
     * @param string    $entityId
     * @param string    $id
     * @param \DateTime $expiryTime
     *
     * @return void
     */
    public function set($entityId, $id, \DateTime $expiryTime)
    {
        $idEntry = $this->manager->find(IdEntry::class, ['entityId'=>$entityId, 'id'=>$id]);
        if (null == $idEntry) {
            $idEntry = new IdEntry();
        }
        $idEntry->setEntityId($entityId)
            ->setId($id)
            ->setExpiryTime($expiryTime);
        $this->manager->persist($idEntry);
        $this->manager->flush($idEntry);
    }

    /**
     * @param string $entityId
     * @param string $id
     *
     * @return bool
     */
    public function has($entityId, $id)
    {
        /** @var IdEntry $idEntry */
        $idEntry = $this->manager->find(IdEntry::class, ['entityId'=>$entityId, 'id'=>$id]);
        if (null == $idEntry) {
            return false;
        }
        if ($idEntry->getExpiryTime()->getTimestamp() < $this->timeProvider->getTimestamp()) {
            return false;
        }

        return true;
    }
}
```

and register it as a service

```yaml
# app/config/services.yml
services:
    id_store:
        class: AppBundle\Store\IdStore
        arguments:
            - "@=service('doctrine').getManager()"
            - "@lightsaml.system.time_provider"
```


## Step 7: Configure symfony bridge

Configure LightSAML Symfony Bridge in your config.yml with own entity id, own credentials, and IDP parties. For the getting
started course LightSAML-Core keys and IDP parties LightSAML are already configured with.

```yaml
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
    store:
        id_state: id_store  # name of id store service created in step 6
```

For advanced configuration check the
[LightSAML Symfony Bridge configuration](/Symfony-Bridge/Configuration)
documentation page.


## Step 8: Update database schema

Update your database with the ``User`` entity schema.

If you are using doctrine migrations, make a diff and migrate

```bash
$ app/console doc:mig:dif
$ app/console doc:mig:mig -n
```

In case you're not using migrations, just force the schema update

```bash
$ app/console doctrine:schema:update --force
```


## Step 9: Define Symfony security user provider

Add entity user provider to your security configuration so Symfony security can read users from the entity
you created in Step 4.

```yaml
# app/config/security.yml
security:
    providers:
        db_provider:
            entity:
                class: AppBundle:User
                property: username
```


## Step 10: Implement User Creator service

In case user provider defined in previous step can not retrieve a user, LightSAML SP Bundle security authentication provider,
if configured so which will be done in next step, will call user creator to create a new user for the given SAML Response.

```php
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
```

Register it as a service

```yaml
# app/config/services.yml
services:
    user_creator:
        class: AppBundle\Security\User\UserCreator
        arguments:
            - "@=service('doctrine').getManager()"
            - "@lightsaml_sp.username_mapper.simple"
```


## Step 11: Configure security

Configure Symfony security firewall to use LightSAML SP Bundle security listener with previously defined user provider and creator.

```yaml
# app/config/security.yml
security:
    firewalls:
        main:
            light_saml_sp:
                provider: db_provider       # user provider name configured in step 9
                user_creator: user_creator  # name of the user creator service created in step 10
                login_path: /saml/login
                check_path: /saml/login_check
            logout:
                path: /logout
            anonymous: ~

    access_control:
        - { path: ^/secure, roles: ROLE_USER }
```

Check the full [LightSAML SP security configuration](../Configuration#security-configuration).


## Step 12: Create homepage and secure pages

Create two pages, one public, other secure, and their corresponding templates, so we can test the security.

```php
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
    public function secureAction()
    {
        return [];
    }
}
```


## Step 13: Test it...

Go to the ``/saml/login`` route. Since in Step 3 we provided two IDP parties, you'll be redirected to a discovery page ``/saml/discovery``
to choose one of them.  When you select IDP, you will be redirect to it's login location with AuthnRequest, and when you authenticate
on that IDP, you'll be redirected back to ``/saml/login_check`` route with Assertion.


## Next Reads

 * [User resolution](../User-resolution/)
 * [Cookbook](../Cookbook/)

