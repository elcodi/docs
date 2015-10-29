# Commands

One of the ways Elcodi brings interaction between the user and the project is by
using the Symfony Console Component. This chapter lists all available commands
in Bamboo provided by Elcodi components and Bamboo.

## elcodi:exchangerates:populate

This command populates the currency exchange rates, using desired populator
adapter.

``` bash
Usage:
  php app/console elcodi:exchangerates:populate [options]

Options:
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```
* Namespace: *Elcodi\Component\Currency\Command\CurrencyExchangeRatesPopulateCommand*
* Service name: *elcodi.command.populate_currency_rates*
* Execution time: ~15 seconds
* [How to add a new Currency Rates Populator adapter](../cookbook/adapters/currency-rates-populator.md)

## elcodi:install

Installs a fresh and basic bamboo instance by using custom fixtures. Installed
project uses fake data in order to recreate how your E-commerce can be if you
work with Elcodi.

``` bash
Usage:
  php app/console elcodi:install [options]

Options:
  -c, --country[=COUNTRY]  Countries to be loaded during the installation [default: ["ES"]] (multiple values allowed)
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Common\CommonBundle\Command\ElcodiInstallCommand*
* Service name: *elcodi.command.elcodi_install*
* Execution time: ~2 minutes
* [Quick start](../quick-start.md)

## elcodi:locations:drop

Drop a set of desired countries, defined by their country iso code.

``` bash
Usage:
  php app/console elcodi:locations:drop [options]

Options:
  -c, --country=COUNTRY    Countries to be loaded, using Iso format (multiple values allowed)
      --drop-if-exists     Drop country if this is already loaded
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Geo\Command\LocationDropCommand*
* Service name: *elcodi.command.location_drop*
* Execution time: It depends on the country size and on the number of countries 
* [How to add a new Location Provider adapter](../cookbook/adapters/location-provider.md)

## elcodi:locations:load

Load a full location tree using an external location source. This command avoids
the process of building all the tree using Doctrine, and simply loads the
country structure by using directly mysql.

``` bash
Usage:
  elcodi:locations:load [options]

Options:
  -c, --country=COUNTRY    Countries to be loaded, using Iso format (multiple values allowed)
      --drop-if-exists     Drop country if this is already loaded
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Geo\Command\LocationLoadCommand*
* Service name: *elcodi.command.location_load*
* Execution time: It depends on the country size and on the number of countries 
* [How to add a new Location Loader adapter](../cookbook/adapters/location-loader.md)

## elcodi:locations:populate

Populates from source a full location tree, by using Doctrine and all the Geo
model entity set. This process can be slow, specially the flush stage.

``` bash
Usage:
  elcodi:locations:populate [options]

Options:
  -c, --country=COUNTRY    Countries to be loaded, using Iso format (multiple values allowed)
      --drop-if-exists     Drop country if this is already loaded
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Geo\Command\LocationPopulateCommand*
* Service name: *elcodi.command.location_populate*
* Execution time: It depends on the country size and on the number of countries 
* [How to add a new Location Populator adapter](../cookbook/adapters/location-populator.md)

## elcodi:metrics:load

Metrics are saved to cache in real time. This is mainly because of the process
that saves these metrics is asynchronous. These metrics are cached to Redis, and
because Redis can be easily flushed, this command caches *n* days of metrics.

``` bash
Usage:
  elcodi:metrics:load <days>

Arguments:
  days                     Number of days you want to load

Options:
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Metric\Core\Command\MetricsLoadCommand*
* Service name: *elcodi.command.metrics_load*
* Execution time: It depends on the number of days loaded.
* [How to add a new Metric Bucket adapter](../cookbook/adapters/metric-bucket.md)

## elcodi:plugin:disable

Disable a plugin by its hash. To check all plugins hash, please check the
command `elcodi:plugin:list`.

``` bash
Usage:
  elcodi:plugin:disable <hash>
  plugin:disable

Arguments:
  hash                     Plugin hash

Options:
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Plugin\Command\PluginDisableCommand*
* Service name: *elcodi.command.plugin_disable*
* Execution time: Less than 1 second
* [Plugins book chapter](../book/plugins.md)

## elcodi:plugin:enable

Enable a plugin by its hash. To check all plugins hash, please check the command
`elcodi:plugin:list`.

``` bash
Usage:
  elcodi:plugin:enable <hash>
  plugin:enable

Arguments:
  hash                     Plugin hash

Options:
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Plugin\Command\PluginEnableCommand*
* Service name: *elcodi.command.plugin_enable*
* Execution time: Less than 1 second
* [Plugins book chapter](../book/plugins.md)

## elcodi:plugin:list

List all installed (both disabled and enabled) plugins with some related
information.

``` bash
Usage:
  elcodi:plugin:list <hash>
  plugin:list

Arguments:
  hash                     Plugin hash

Options:
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Plugin\Command\PluginListCommand*
* Service name: *elcodi.command.plugins_list*
* Execution time: Less than 1 second
* [Plugins book chapter](../book/plugins.md)

## elcodi:plugin:load

Load all plugins to the system. This is the way Bamboo knows the existance of
installed packages with plugins features. Once this command is executed, then
you will be able to manage them all in your admin panel

``` bash
Usage:
  elcodi:plugins:load
  plugin:load

Options:
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -s, --shell              Launch the shell.
      --process-isolation  Launch commands from shell as a separate process.
  -e, --env=ENV            The Environment name. [default: "dev"]
      --no-debug           Switches off debug mode.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Plugin\Command\PluginLoadCommand*
* Service name: *elcodi.command.plugins_load*
* Execution time: Less than 1 second
* [Plugins book chapter](../book/plugins.md)

## elcodi:sitemap:dump

Dumps an entire sitemap.

``` bash
Usage:
  elcodi:sitemap:dump [options] [--] <builder-name> <basepath>

Arguments:
  builder-name               Builder name
  basepath                   Base path

Options:
      --language[=LANGUAGE]  Language
  -h, --help                 Display this help message
  -q, --quiet                Do not output any message
  -V, --version              Display this application version
      --ansi                 Force ANSI output
      --no-ansi              Disable ANSI output
  -n, --no-interaction       Do not ask any interactive question
  -s, --shell                Launch the shell.
      --process-isolation    Launch commands from shell as a separate process.
  -e, --env=ENV              The Environment name. [default: "dev"]
      --no-debug             Switches off debug mode.
  -v|vv|vvv, --verbose       Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Sitemap\Command\SitemapDumpCommand*
* Service name: *elcodi.command.sitemap_dump*
* Execution time: Less than 1 second
* [Sitemap Component](../component/sitemap.md)

## elcodi:sitemap:profile

Builds a sitemap profile

``` bash
Usage:
  elcodi:sitemap:dump [options] [--] <builder-name> <basepath>

Arguments:
  builder-name               Builder name
  basepath                   Base path

Options:
      --language[=LANGUAGE]  Language
  -h, --help                 Display this help message
  -q, --quiet                Do not output any message
  -V, --version              Display this application version
      --ansi                 Force ANSI output
      --no-ansi              Disable ANSI output
  -n, --no-interaction       Do not ask any interactive question
  -s, --shell                Launch the shell.
      --process-isolation    Launch commands from shell as a separate process.
  -e, --env=ENV              The Environment name. [default: "dev"]
      --no-debug             Switches off debug mode.
  -v|vv|vvv, --verbose       Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

* Namespace: *Elcodi\Component\Sitemap\Command\SitemapProfileCommand*
* Service name: *elcodi.command.sitemap_profile*
* Execution time: Less than 1 second
* [Sitemap Component](../component/sitemap.md)