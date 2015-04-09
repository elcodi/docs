SitemapBundle Reference
=======================

```yaml
elcodi_sitemap:
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
    # Each static is a route entry
    statics:
        store_homepage:
            # Specific change frequency for this static
            changeFrequency: ~
            # Specific priority for this static
            priority: ~
    # A builder is a set of blocks and statics, grouped and saved in a file
    builder:
        main:
            # You can define the name of the file, taking in account the locale
            # used by using {_locale} format
            path: %kernel.root_dir%/../web/sitemap/sitemap_{_locale}.xml
            # Each builder can use a different renderer
            renderer: ~
            # Set of block references
            blocks:
                - enabled_products
                - enabled_categories
            # Set of static references
            statics:
                - store_homepage
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
