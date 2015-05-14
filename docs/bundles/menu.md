Menu
====

This bundle is intended to build menus in a very simple way for your 
administrators or websites.

> Please, check the Sitemap Component documentation, in order to understand the
> types of modificators or changers that you can use for this

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

``` yaml
services:
    elcodi.menu_builder:
        class: Elcodi\Component\Menu\Services\MenuBuilder
        tags:
            - { name: menu.changer }

    elcodi.menu_filterer:
        class: Elcodi\Component\Menu\Services\MenuFilterer
        tags:
            - { name: menu.changer }

    elcodi.menu_modifier:
        class: Elcodi\Component\Menu\Services\MenuModifier
        tags:
            - { name: menu.changer }
```

So it means that, first of all, all Builders are executed. Then, all Filters are
applied, and finally, all Modifiers are applied.