PUGX CMF Page Bundle
=========================

This package is a Symonfy2 bundle that provides pages content management system for Symfony2 built on top of [Symfony CMF](http://cmf.symfony.com) and [Sonata Admin](https://sonata-project.org/bundles/admin/2-3/doc/index.html) bundle.

This bundle takes some important decision about persistence, usability and features exposed to the content management user. Please, take into consideration that these decisions could not fit your needs and feel free to override these implementations in your application.

This bundle provides only an admin interface to manage **Pages** and related **Menu Nodes**. The user is not able to manage **Routes** which are auto generated by configuration. The persistence layer used by this bundle is **Doctrine PHPCR**.

Pages
-----
All **Pages** are placed under `/cms/content` path of the PHPCR repositotory and the user cannot create Pages hierarchly (all pages are created on the same level). The PHPCR node name of every page is auto generated from page's title. For example, if you create a page with "About Us" title, its PHPCR path will be `/cms/content/about-us`. Pages's Routes are auto generated (through CMF AutoRoutingBundle) and are placed under `/cms/routes` path of PHPCR repository. The route is autogenerated with a slug version of Page's title. For example, the route for the "About Us" page is created at `/cms/routes/about-us` PHPCR path. `/cms/routes` is also the base path for CMF's dynamic routing so the "About Us" page is reachable at URL *http://mydomain.com/about-us*. The user is not able to change routes for pages; only autogeneration is available.

Menu Nodes
----------
All **Menu Nodes** are placed under `/cms/menu` path of the PHPCR repository. This bundle defines a custom initializer that creates the **Menus** defined in configuration. So, for example, if you configure `{main: Main Menu, footer: Footer Menu}` menus, they will be created under `/cms/menu`. The user is able to create menu nodes hierachly (under one of the confiugred menus). Menu Nodes can refer content under `/cms/content` or can point to custom URI. The user can manage Page's Menu Nodes directly from Page form.

Compatibility
-------------

This bundle has been tested on the following dependencies.

```
"php": ">=5.3.3",
"symfony/symfony": "~2.5",
"doctrine/doctrine-bundle": "~1.2",
"doctrine/data-fixtures": "~1.0",
"doctrine/doctrine-cache-bundle": "~1.0",
"twig/extensions": "~1.0",
"symfony/assetic-bundle": "~2.3",
"symfony/swiftmailer-bundle": "~2.3",
"symfony/monolog-bundle": "~2.4",
"sensio/distribution-bundle": "~3.0",
"sensio/framework-extra-bundle": "~3.0",
"incenteev/composer-parameter-handler": "~2.0",
"nelmio/alice": "~1.0",
```

Installation
------------

Install Symfony2 standard edition and then add the dependency to this bundle in your `composer.json`:

```bash
$ composer require pugx/cmf-page-bundle
```

You also have to add `downloadCreateAndCkeditor` composer script handler to your `composer.json`:

```
	// ...
    "scripts": {
        "post-install-cmd": [
        	// ...
        	"Symfony\\Cmf\\Bundle\\CreateBundle\\Composer\\ScriptHandler::downloadCreateAndCkeditor"
        ],
        "post-update-cmd": [
        	// ...
        	"Symfony\\Cmf\\Bundle\\CreateBundle\\Composer\\ScriptHandler::downloadCreateAndCkeditor",
        ]
    },
    // ...
```
And run `composer update pugx/cmf-page-bundle`.

Now you have to add Symfony CMF bundles and `PUGXCmfPageBundle` to your `AppKernel`:

```
    public function registerBundles()
    {
        $bundles = array(
        	// ...
        	// Symfony CMF Bundles
            new Doctrine\Bundle\PHPCRBundle\DoctrinePHPCRBundle(),
            new Doctrine\Bundle\DoctrineCacheBundle\DoctrineCacheBundle(),
            new Symfony\Cmf\Bundle\RoutingBundle\CmfRoutingBundle(),
            new Symfony\Cmf\Bundle\RoutingAutoBundle\CmfRoutingAutoBundle(),

            new Symfony\Cmf\Bundle\BlockBundle\CmfBlockBundle(),
            new Sonata\BlockBundle\SonataBlockBundle(),
            new Sonata\CoreBundle\SonataCoreBundle(),
            new Sonata\jQueryBundle\SonatajQueryBundle(),
            new Sonata\AdminBundle\SonataAdminBundle(),
            new Sonata\DoctrinePHPCRAdminBundle\SonataDoctrinePHPCRAdminBundle(),

            new Symfony\Cmf\Bundle\MenuBundle\CmfMenuBundle(),
            new Knp\Bundle\MenuBundle\KnpMenuBundle(),

            new Symfony\Cmf\Bundle\CreateBundle\CmfCreateBundle(),
            new FOS\RestBundle\FOSRestBundle(),
            new JMS\SerializerBundle\JMSSerializerBundle(),

            new PUGX\Cmf\PageBundle\PUGXCmfPageBundle(),
            new Symfony\Cmf\Bundle\CoreBundle\CmfCoreBundle(),
        );
        
        // ...
```

Pay attention that `CmfCoreBundle` must be loaded after any bundle which prepend config (such as `PUGXCmfPageBundle`) because in `CmfCoreExtension::prepend()` it copies configuration for others CMF bundles. So because `PUGXCmfPageBundle` would prepend configuration for `cmf_core` it must be loaded before `CmfCoreBundle` to correctly copy such config on other CMF bundles.

Then add the routing to your application in the `app/config/routing.yml`:

```
pugx_cmf_page_bundle:
    resource: "@PUGXCmfPageBundle/Resources/config/routing.yml"
    type: yaml
```

License
-------
This bundle is under the MIT license. See the complete license text [here](http://opensource.org/licenses/MIT).
