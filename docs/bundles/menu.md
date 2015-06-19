Menu
====

Use and configure the Menu component in your Symfony project.

* [Component Documentation](http://elcodi.io/docs/components/menu/)
* [Github Repository](https://github.com/elcodi/MenuBundle)

## Using the DIC

Exposing your Menu changers (Filters, Builders and Modifiers) is so simple using
the Symfony Dependency Injection definitions. For this purpose, this bundle is
built on top the tags feature, allowing to define a simple changer just adding
a simple tag.

By default, this Bundle has three changers defined and enabled.

``` yaml
services:

    #
    # Menu filters
    #
    elcodi.menu_filter.menu_disabled:
        class: Elcodi\Component\Menu\Filter\MenuDisabledFilter
        tags:
            - { name: menu.filter }

    #
    # Menu modifiers
    #
    elcodi.menu_modifier.menu_active:
        class: Elcodi\Component\Menu\Modifier\MenuActiveModifier
        arguments:
            - @request_stack
        tags:
            - { name: menu.modifier }

    elcodi.menu_modifier.menu_expanded:
        class: Elcodi\Component\Menu\Modifier\MenuExpandedModifier
        arguments:
            - @request_stack
        tags:
            - { name: menu.modifier }
```

As you can see, there is no any Builder defined here. Let's see an example of a
Builder definition.

``` yaml
services:

    #
    # Menu builders
    #
    store.plugin.menu_builder.plugins:
        class: Elcodi\Admin\PluginBundle\MenuBuilder\PluginMenuBuilder
        arguments:
            - @elcodi.factory.menu_node
            - @router
            - @elcodi.enabled_plugins
        tags:
            - { name: menu.builder }
```

You can define as much elements you need.

## Workflow

The order matters. It means that is not the same to apply all the filters first
of all and then execute the modifiers, or applying the other way.

This bundle define all changers in this order

* Builders
* Filters
* Modifiers

## Cache

Each changer can be used before or after cache by setting its stage. Please, 
read the [component documentation](http://elcodi.io/docs/components/menu/) to 
understand what means this configuration and what is indended for.

``` yaml
services:

    #
    # Menu builders
    #
    store.plugin.menu_builder.some_builder:
        class: Namespace\Of\Menu\Builder
        tags:
            - { name: menu.builder, stage: before_cache }
            - { name: menu.builder, stage: after_cache }
```

By default, `before_cache` will be used.

## Specific menus

Each changer can be assigned to a set of menus by using the menus property in 
the tag definition.

``` yaml
services:

    #
    # Menu builders
    #
    store.plugin.menu_builder.some_builder:
        class: Namespace\Of\Menu\Builder
        tags:
            - { name: menu.builder, menus: ['admin', 'store'] }
```

By default, all menus will be assigned.

## Priority

Each group will prioritize all its instances by using priority. You can set a 
priority using the tag.

``` yaml
services:

    #
    # Menu builders
    #
    store.plugin.menu_builder.some_builder:
        class: Namespace\Of\Menu\Builder
        tags:
            - { name: menu.builder, priority: 10 }
```

By default, priority 0 will be used.

> The more priority, the before this changer is applied. Negative values are
> kindly allowed here.