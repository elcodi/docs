# Plugins

Create, install and manage several plugins in your Bamboo installation.

* [Component Repository](https://github.com/elcodi/Plugin)
* [Bundle Repository](https://github.com/elcodi/PluginBundle)

We want to introduce you what is a Plugin when we talk about Elcodi. Indeed,
this question has a very fast abstract answer that will, surely, give you some
extra calmness.

A Plugin is a Bundle.

Yes, when we talk about Plugins, we talk about Symfony Bundles, with a simple 
and pre-defined specification. And is this specification what we will show you
properly, in order to help you understanding existing plugins and composing new
ones.

## What is a Bundle?

Like any Bundle, a Plugin needs to have its own Bundle class. This is usually
placed in the root of the bundle, and should be called as much specifically as
possible, in order to avoid collisions.

> Even if two bundles share the same name, the uniqueness of each Plugin is 
> defined by its namespace, so even in this scenario, your Elcodi installation
> will work properly.

Let's see an example of a bundle. We will work along this document with our
first Plugin, the DisqusBundle.

``` php
namespace Elcodi\Plugin\DisqusBundle;

use Symfony\Component\HttpKernel\Bundle\Bundle;

use Elcodi\Component\Plugin\Interfaces\PluginInterface;

/**
 * Class ElcodiDisqusBundle
 */
class ElcodiDisqusBundle extends Bundle implements PluginInterface
{
    // ...
}
```

Implementing `PluginInterface` is the way the kernel has to identify if a bundle
has to be treated as a Plugin

## Why plugins?

That's one of the most important parts of our philosophy. We will give some 
reasons to have a plugin-driven design.

* Because you will design decoupled from the core. This means that, if you
update Elcodi and Bamboo with new features (remember, using semantic version),
there wont be the possibility to have conflicts. You only develop your own 
plugins, based on the master project.
* Because you can share. If you overwrite some parts of the project proposing
new business logic, and if you take care about related documentation, then you
can share your work, and even sell it.
* Because will help you with the development of the package. Your code will be
cleaner and more testable... and this is always good news.
* Because you will follow Elcodi style. All these proposed reasons is what makes
Elcodi a good project to work with. Following our structure will help this
ecosystem to grow day by day, and this is what will make Elcodi a successful 
project indeed.

## Plugin dependencies

All plugins are extending the main project, but some of them have some 
dependencies. Remember that Elcodi has it's own bundle dependencies resolver.
Please, read careful this chapter in order to understand how to define properly
these dependencies.

[How to Install dependent Bundles](http://elcodi.io/docs/cookbook/installation/install-dependent-bundles/)

## Plugin definition

There is a very simple way to define a Plugin. What is the name of the plugin, 
what are the fields that will configure it, and some more information is what
you will have to place in the `plugin.yml` file.

> This file must be placed in the root of the Bundle

Let's see an example of how the DisqusPlugin is configured.

``` yaml
plugin:
    type: plugin
    category: comments
    name: Discuss
    description: |
        Disqus integration for your shop
    fa_icon: comment-o
    fields:
        disqus_identifier:
            type: text
            label: elcodi_plugin.disqus.disqus_identifier
        disqus_enabled_product:
            type: checkbox
            label: elcodi_plugin.disqus.disqus_enabled_product
        disqus_enabled_blog_post:
            type: checkbox
            label: elcodi_plugin.disqus.disqus_enabled_blog_post
```

Each definition is considered as a configuration element, and all these are
available.

* type: You must define your Plugin as a simple plugin or as a template. Of 
course this value is required. Available values are `plugin` or `template`.
* category: You can tag it as well using a specific category. Each category is
meant to be treated in a different specific way. Specific and useful values
are `payment`, `shipping` and `social`. If none value is specified, then will
not belong to any category.
* name: The name of your plugin
* visible: This plugin will be visible for its configuration. By default, this
value is set as true.
* fa_icon: Icon used, following the [Awesome Fonts](http://fontawesome.io/) 
format
* description: Plugin description
* fields: Set of configurable fields

## Plugin fields

What is a field? Well, your plugins may require some values to be stored in
your database related to the plugin. For example, an `api_public_key` or a 
simple `username`.

These values are stored in a plain way, and because is important to make easy
the management of these values, Bamboo will provide automatically your Plugin
configuration page, only by defining how these fields should be created. These
are all possibilities

* type: Type of the field. This value will be used as field type in form
* required: This field must be configured in order to use this plugin
* data: The value of the field
* label: Label of the field (You can use translations)
* options: Array of options, needed if your field is checkbox or radio.
* attr: Array of attributes

Let's see an example. If our plugin require a text field called username...

``` yaml
plugin:
    type: plugin
    category: social
    name: Twitter
    description: |
        Twitter integration for your shop
    fa_icon: twitter-o
    fields:
        username:
            type: text
            label: elcodi_plugin.twitter.username
            required: true
```

With this example, your Bamboo installation will provide you a nice page with
a very simple way of defining the twitter username.

A little bit later you will find the way of retrieving these values. 

## Dependency Injection

A plugin is a Bundle, remember? This means that you should define a set of 
services in the same way you do when you create your amazing bundles.

You can follow the Elcodi way by reading some cookbooks in this documentation,
so you will make sure that everybody will understand more easily what you're 
trying to do.

* [How to install dependent Bundles](http://elcodi.io/docs/cookbook/installation/install-dependent-bundles/)
* [Extensions](http://elcodi.io/docs/book/standards/#bundles)
* [Some Dependency Injection standards](http://elcodi.io/docs/book/standards/dependency-injection/)

Remember to follow some of our philosophy. Small services and well defined.
Clean code is important. This part is the most important part of your Plugin.
Preserve each service responsibility and be sure everyone will understand what
is all services purpose.

## Plugin as a service

Sometimes you need to inject your plugin, right? Well, you can do that as long
as you can reach your plugin using the `PluginRepository` and you can define the
result content as a service.

``` yaml
services:

    #
    # Plugin
    #
    elcodi_plugin.disqus:
        parent: elcodi.abstract_plugin
        arguments:
            - "Elcodi\\Plugin\\DisqusBundle\\ElcodiDisqusBundle"
```

You need to be as specific as possible to avoid collisions between different 
bundles. Of course you can not determine that your Disqus plugin will be the 
only one installable, so has no sense to name your plugin service `disqus`, but
maybe something like that... `{my_organization}_elcodi_plugin.disqus`

Once you have your service, then you can do that.

``` yaml
services:

    #
    # Twig renderer
    #
    elcodi_plugin.disqus.renderer:
        class: Elcodi\Plugin\DisqusBundle\Templating\TwigRenderer
        calls:
            - [setPlugin, [@elcodi_plugin.disqus]]
```

In your class, then you receive your plugin object (plugin entity). Let's see an
example about what this class allows you to manage.

``` php
use Elcodi\Component\Plugin\Entity\Plugin;

/**
 * Class MyService
 */
class MyService
{
    /**
     * @var Plugin
     *
     * Plugin
     */
    protected $plugin;
    
    /**
     * Construct
     *
     * @param Plugin $plugin Plugin
     */
    public function __construct(Plugin $plugin) 
    {
        $this->plugin = $plugin;
    }
    /**
     * Add Stripe payment method
     *
     * @param PaymentCollectionEvent $event Event
     */
    public function addStripePaymentMethod(PaymentCollectionEvent $event)
    {
        if ($this
            ->plugin
            ->isUsable([
                'private_key',
                'public_key',
            ])
        ) {
            // I can do whatever I want here. My plugin is usable!
        }
    }
}
```

Each plugin has some interesting public methods. Let's see some of them

### getConfigurationValue()

You require a configuration element. Please, see the full list in the Plugin
definition chapter.

``` php
$pluginCategory = $this
    ->plugin
    ->getConfigurationValue('category');
```

### getFieldValue()

Remember our last example? We configured one field called username for our
Twitter plugin, right? Well, let's get the username value by using this cool
method.

``` php
$username = $this
    ->plugin
    ->getField('username');
```

### getField()

Sometimes you need to fetch all the field data, including it's definition and
value. Then, you can use this method. You receive an array with all the data, 
and the value is always placed in the hash position with name `data`.

``` php
$usernameField = $this
    ->plugin
    ->getField('username');
    
$username = $usernameField['data'];
```

### getFields()

If you need all plugin's fields as an array, then you can call this public
method. You receibe an array of fields, so you have to access the same way than
before.

``` php
$allFields = $this
    ->plugin
    ->getFields();
    
$username = $allFields['username']['data'];
```

### getFieldValues()

You can request as well all fields with their values using a simple key-value
structure, being the key the name of the field and the value, the value of the 
field. If this is what you need, then use this method.

``` php
$allValues = $this
    ->plugin
    ->getFieldValues();
    
$username = getFieldValues['username'];
```

### isUsable

You can check if a Plugin is usable by checking that is enabled and that a set
of defined fields are fulfilled. A field is fulfilled when is not required or
is required and the field is not empty.

If you don't pass any field, then `enabled` field is checked.

```
$isUsable = $this
    ->plugin
    ->isUsable(['username']);
```

In this example, and because the twitter plugin should not work unless the 
username is set (username field is defined as required), then will return true
only if the plugin itself is enabled and the field username is not empty.

### guessIsUsable

Well, all this work can be done automatically, without need to define all
fields. So if you use this method, then all fields will be checked, so a plugin
will be usable only if is usable using all plugin fields.

That means that all required fields defined in the plugin configuration must
have a value.

```
$isUsable = $this
    ->plugin
    ->gessIsUsable();
```

In this example, and because our twitter plugin only have one single field and
is required, the result will be exactly the same than the last piece of code.

## Installing your Plugin

You can do that using the command console by using the `elcodi:plugins:load`
command. If you have configured your plugin the right way, then you should have
your Plugin configuration panel in the admin side.

[How to Install a plugin](http://elcodi.io/docs/cookbook/installation/install-a-plugin/)

After loading the plugin, of course, you need to add it in your AppKernel file.
Because some plugins have dependencies and because it's main mission is 
overwriting logic, you should instantiate them at the end of the class.

``` php
use Symfony\Component\Config\Loader\LoaderInterface;
use Symfony\Component\HttpKernel\Kernel;
use Symfony\Component\HttpKernel\Bundle\BundleInterface;

use Elcodi\Bundle\CoreBundle\Traits\BundleDependenciesResolver;

/**
 * Class AppKernel
 */
class AppKernel extends Kernel
{
    use BundleDependenciesResolver;
    
    /**
     * Returns an array of bundles to register.
     *
     * @return BundleInterface[] An array of bundle instances.
     *
     * @api
     */
    public function registerBundles()
    {
        $bundles = array(
        
            /**
             * All Symfony, Bamboo and Elcodi bundles
             * with their dependencies
             */
            
            // ...
            
            /**
             * Elcodi Plugins
             */
            new My\Elcodi\Plugin(),
            new My\Second\Elcodi\Plugin(),
        );
        
        return $this
            ->getBundleInstances(
                $this,
                $bundles
            );
    }
}
```

## Overwriting model

One of the main goals of a plugin is overwriting the model. Of course you will
have to overwrite your desired entities, and old ones will not be used anymore
as the main entity (unless you extend them, of course).

You can take a look at these chapters in order to understand properly how easy
is overwriting the Elcodi model.

[How to implement an entity](http://elcodi.io/docs/cookbook/implementation/implement-an-entity/)
[How to overwrite an entity](http://elcodi.io/docs/cookbook/overwrite/overwrite-an-entity/)

If a plugin overwrites a new entity or creates new entities that depends on 
existing ones, then these dependant bundles that own these entities become 
dependencies of the plugins itself. Ensure that they are defined as such.

## Overwriting services

Like model, service layer is overwritable by using the same philosophy than the
Symfony way. You will find some nice information in the Symfony documentation
about how to overwrite any part of the bundle, and in some of our cookbooks.

[How to Override any Part of a Bundle](http://symfony.com/doc/current/cookbook/bundles/override.html)
[How to overwrite a service](http://elcodi.io/docs/cookbook/overwrite/overwrite-a-service/)

Of course, you can overwrite some parameters

[How to overwrite a parameter](http://elcodi.io/docs/cookbook/overwrite/overwrite-a-parameter/)

## Adding configuration

One of the main plugin responsibilities (and needs many times) is to add
configuration values into the main project. This means that not only should be
responsible of adding it's own configuration but add configuration related to
other bundles.

There is a very easy way of doing this, assuming that the bundle you need to
configure is already a dependency of your plugin, or at least that your plugin
is being loaded after the configurable bundle in the kernel file.

You only have to create a new file called `external.yml` inside the folder
`@MyPlugin/Resources/config/external.yml` and add all the configuration there.

This file will be appended by a Compiler Pass into the master config file, so
after compiled, the container will take in account this data as well.

## Adding routes

Another useful action you might need inside a Plugin is to add some routes in
the master routing file.

In fact, you don't need to modify the routing master file to add new routes. You
only need to create a new file called `routing.yml` inside the folder
`@MyPlugin/Resources/config/routing.yml` and after flushing the cache, your
project will automatically detect new routes.

## Adding listeners

Part of a plugin should be as well adding some new cool behavior to your
project, and the cleanest way of doing that is by adding some listeners to our
events.

Please, take a look at our event list.

[Elcodi Events](http://elcodi.io/docs/book/events/)
