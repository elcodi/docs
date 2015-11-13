How to Install a plugin
=======================

To read about plugins, please read all the 
[Plugins Chapter](http://elcodi.io/docs/book/bundles/plugins/) of our book.

To install a plugin, you must treat it as a simple third party bundle.

* Add the plugin (bundle) in your composer dependencies
* Update your dependencies by using *composer update*
* Add the bundle in your kernel

But because a Plugin is not only a Bundle, this is not enough. Bamboo offers you
a very easy way of updating the information of your plugins by using a command.
It only takes 2 seconds!

``` bash
php app/console elcodi:plugins:load
```

Your already loaded plugin data will not be updated unless some plugin has 
changed its configuration structure. Please pay attention to your dependencies
versions.

After this command, your will be able to manage your plugins through your admin
panel.