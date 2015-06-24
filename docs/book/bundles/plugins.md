Plugins
=======

We want to introduce you what is a Plugin when we talk about Elcodi. Indeed,
this question has a very fast abstract answer that will, surely, give you some
extra calmness.

A Plugin is a Bundle.

Yes, when we talk about Plugins, we talk about Symfony Bundles, with a simple 
and pre-defined specification. And is this specification what we will show you
properly, in order to help you understanding existing plugins and composing new
ones.

### Bundle

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

### Definition

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

You can define these items:

* type: You must define your Plugin as a simple plugin or as a template.
* category: You can tag it as well. Social, comments, payment...
* name: The name of your plugin
* visible: This plugin will be visible to be configured
* fa_icon: Icon used, following the Awesome Fonts format
* description: Plugin description
* fields: Set of configurable fields

Each field can be defined as follows:

* type: Type of the field. This value will be used as field type in form
* required: This field must be configured in order to use this plugin
* data: The value of the field
* label: Label of the field (You can use translations)
* options: Array of options, needed if your field is checkbox or radio.
