# Sitemap

Use and configure the Sitemap component in your Symfony project.

* [Component Repository](https://github.com/elcodi/Sitemap)
* [Bundle Repository](https://github.com/elcodi/SitemapBundle)

The bundle is built in different layers, going from the real seed of any kind
of sitemap implementation (each element), to the final profiling.

## What offers

This package should work as a black box, so some configuration and some classes
to implement the way some entities are searched or are transformed should be
enough.

The package provides you as well a simple set of generated services and commands
to make easier the generation of the documents. Read this documentation for more
information about how you can generate simple and complex Sitemap documents.

> Can be useful as well for creating integration files with external 
> marketplaces, for example, Google Products.

### Blocks

On a sitemap, a block is the element that we would like to index. If a product
has a page then we would like our sitemap to contain an entry url per product 
page. The items to index in a sitemap can be pages of products, categories, and 
any other entity on the project.

When we talk about a block, we are really talking about a specific set of urls
related to entities provided by a specific repository, for example all enabled 
products.

``` yaml
# Each block defines a way of creating dynamically a set of elements of a
# sitemap file, each one mapped from a database entry
blocks:
    enabled_products:
        # Transformer
        transformer: ~
        # Repository service used for retrieving the array of entities
        repository_service: elcodi.repository.product
        # Repository method
        method: findBy
        # Array of arguments for the repository call
        arguments:
            enabled: true
        # Specific change frequency for this block
        changeFrequency: ~
        # Specific priority for this block
        priority: ~
```

Each block needs a transformer, intended to transform an entity instance to 
every single information needed by the sitemap generator. Every transformer must
be an implementation of SitemapTransformerInterface and must be defined as a
service in our Dependency Injection Container.

``` php
/**
 * Interface SitemapTransformerInterface
 */
interface SitemapTransformerInterface
{
    /**
     * Get url given an entity
     *
     * @param Mixed  $element  Element
     * @param string $language Language
     *
     * @return string url
     */
    public function getLoc($element, $language = null);

    /**
     * Get last mod
     *
     * @param Mixed  $element  Element
     * @param string $language Language
     *
     * @return string Last mod value
     */
    public function getLastMod($element, $language = null);
}
```

Then we need to pass the name of the service to the block configuration.

``` yaml
# Each block defines a way of dynamically creating a set of elements of a
# sitemap file, each one mapped from a database entry
blocks:
    enabled_products:
        # Transformer
        transformer: elcodi.sitemap_transformer.product
```

> This element is a mandatory and must be defined for each Block

That said, we need to also define how this product collection must be
retrieved from our database. For this reason you must specify the
repository service, the method used and the arguments for such method.

``` yaml
# Each block defines a way of dynamically creating a set of elements of a
# sitemap file, each one mapped from a database entry
blocks:
    enabled_products:
        repository_service: elcodi.repository.product
        # Repository method
        method: findBy
        # Array of arguments for the repository call
        arguments:
            enabled: true
            disabled: false
```

These elements are required and have no default values, to make sure
that every block definition is carefully defined and configured. 

Finally, we can define the `changeFrequency` and the `priority` elements. Both
values will be used for all block instances. They are not required.

``` yaml
# Each block defines a way of dynamically creating a set of elements of a
# sitemap file, each one mapped from a database entry
blocks:
    enabled_products:
        # Specific change frequency for this block
        changeFrequency: ~
        # Specific priority for this block
        priority: ~
```

### Statics

An Static is a representation of a route, defined by its name and some 
non-required values.

``` yaml
elcodi_sitemap:

    # Each static is a route entry
    statics:
        store_homepage:
            # Specific change frequency for this static
            changeFrequency: ~
            # Specific priority for this static
            priority: ~
```

### Builders

A sitemap instance is built using a sorted set of Statics and Blocks and
rendered by a specific renderer. The result of this built is dumped using a
specific dumper in a pre-defined path.

``` yaml
elcodi_sitemap:
    
    # A builder is a set of blocks and statics, grouped and saved to a file
    builder:
        main:
            # Set of block references
            blocks:
                - enabled_products
            # Set of static references
            statics:
                - store_homepage
            # Each builder can use a different renderer, by referencing the
            # service definition
            renderer: ~
            # Each builder can use a different dumper, by referencing the
            # service definition
            dumper: ~
            # You can define the name of the file, taking into account the locale
            # used by using {_locale} format
            path: %kernel.root_dir%/../web/sitemap/sitemap_{_locale}.xml
```

First of all we need to define what blocks and statics we are going to use for
such sitemap. It is important to know that the order matters.

For example, in our case, we will create a new sitemap called `main` that will
contain a block and a static.

``` yaml
elcodi_sitemap:
    
    # A builder is a set of blocks and statics, grouped and saved to a file
    builder:
        main:
            # Set of block references
            blocks:
                - enabled_products
            # Set of static references
            statics:
                - store_homepage
```

> This component will first render the statics and then the blocks.

Then, a renderer will transform this set of entries into a special format. By 
default this component gives you one renderer implementation, the `XmlRenderer`.
This implementation is the one used by default.

``` yaml
elcodi_sitemap:
    
    # A builder is a set of blocks and statics, grouped and saved to a file
    builder:
        main:
            # Each builder can use a different renderer, by referencing the
            # service definition
            renderer: ~
```

If you want to use your own renderer, you can create a new service implementing 
`SitemapRendererInterface`.

``` php
/**
 * Interface SitemapRendererInterface
 */
interface SitemapRendererInterface
{
    /**
     * Given an array of sitemapElements, renders the Sitemap
     *
     * @param SitemapElement[] $sitemapElements Elements
     * @param string           $basepath        Base path
     *
     * @return string Render
     */
    public function render(array $sitemapElements, $basepath);
}
```

Finally, a dumper will dump your rendered data. By default this component gives
you one dumper implementation, the `FilesystemDumper`. This implementation is 
the one used by default.

``` yaml
elcodi_sitemap:
    
    # A builder is a set of blocks and statics, grouped and saved to a file
    builder:
        main:
            dumper: ~
            # You can define the name of the file, taking in account the locale
            # used by using {_locale} format
            path: %kernel.root_dir%/../web/sitemap/sitemap_{_locale}.xml
```

If you want to use your own dumper, you can create a new dumper implementing
`SitemapDumperInterface`.

``` php
/**
 * Interface SitemapDumperInterface
 */
interface SitemapDumperInterface
{
    /**
     * Dumps a sitemap given a path
     *
     * @param string $path    Path
     * @param string $sitemap Sitemap
     *
     * @return $this Self object
     */
    public function dump($path, $sitemap);
}
```

This dumper uses a path. This path is required by any defined builder and can
contain a placeholder for the sitemap language, using `{_locale}`.

### Services

Elcodi generates a Dependency Injection service for each builder using our
standard. In our example, with given configuration we will have available a new
public service.

``` yaml
elcodi.sitemap_builder.main
```

Calling this service, the DI will create a SitemapBuilder instance, ready
to be used.

``` php
/**
 * Class SitemapBuilder
 */
class SitemapBuilder
{
    /**
     * Build sitemap builder
     *
     * @param string      $basepath Base path
     * @param string|null $language Language
     *
     * @return string Generated data
     */
    public function build($basepath, $language = null);
}
```

Elcodi also generates a service for each dumper, using the same notation as
builders.

``` yaml
elcodi.sitemap_dumper.main
```

Calling this service, the DI will create a SitemapDumper instance, ready
to be used.

/**
 * Class SitemapDumper
 */
class SitemapDumper
{
    /**
     * Dump builder using a dumper
     *
     * @param string      $basepath Base path
     * @param string|null $language Language
     */
    public function dump($basepath, $language = null);
}

As you can see, both services require the basepath of the site. It means that
you can call, for example, the `build` method using firstly the basepath 
`http://localhost:8000` and then `https://myurl.com`. Because this information
does not belong to the instance but the call, it is mandatory to set this
information when the sitemap is generated.

You must define the language as well, but this second parameter is not required.
This value is passed to the transformer, and your transformer implementation 
will decide what to do with this language.

### Dumper Command

This command is intended for a single sitemap generation. You must provide the
name of the builder as each builder has an associated dumper and a specific 
basepath used for this built and an optional language.

``` bash
php app/console elcodi:sitemap:dump main http://localhost:8000
php app/console elcodi:sitemap:dump main https://myurl.com
php app/console elcodi:sitemap:dump main http://localhost:8000 --language=es
```

### Profiles

Using the dumper is so nice and easy if you just manage static languages, but
how about if our site needs to generate several builders using, for example, all 
the enabled languages of our site?

In order to solve this common problem, this bundle provides as well what we call
a sitemap profile.

``` yaml
elcodi_sitemap:

    # A profile is a set of builders, grouped and combined with all available
    # languages. Special for bulk actions
    profile:
        main:
            # Service reference, result which is an array of languages
            languages: elcodi.languages_iso_array
            # Set of builder references
            builders:
                - main
```

There are two elements to be defined here, both required.

First, we need to decide what builders we want to include in this profile.
This is just a set of builders, an array. That easy.

Second, we must define a service in our project, intended to return all enabled
languages in our site. This service must return an array of locales.

In elcodi we have a service called `elcodi.languages_iso`.

``` yaml
elcodi.languages_iso_array:
    class: stdClass
    factory: [@elcodi.languages_iso, toArray]
```

> Yes, in Symfony a service can use the factory pattern to return a simple 
> array using the stdClass keyword.

Elcodi generates a service for each profile, using our standard. In our example, 
with the given configuration we will have available a new public service.

``` yaml
elcodi.sitemap_profile.main
```

Calling this service, the DI will create a SitemapProfile instance, ready
to be used.

``` php
/**
 * Class SitemapProfile
 */
class SitemapProfile
{
    /**
     * Build full profile
     *
     * @param string $basepath Basepath
     *
     * @return $this Self object
     */
    public function dump($basepath);
}
```

### Profile Command

This command is intended for profile generation. You must provide the
name of the profile and a specific basepath used for this build.

``` bash
php app/console elcodi:sitemap:profile main http://localhost:8000
php app/console elcodi:sitemap:profile main https://myurl.com
```
