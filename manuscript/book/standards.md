# Elcodi Standards

We have noticed that everyone has a different way of developing. In our team, 
some people place the braces at the end of the class definition, while other
people place them in a new line.

Is anyone wrong? Absolutely not, even PHP has standards since long time
ago, and besides anyone should also be able to work the way they want.

But in Elcodi we have some standards for our code.

## Packages Structure

A component must be treated as a simple PHP library. This means that it must be
framework agnostic. At the beginning, Elcodi was a set of Bundles, divided by
concerns or boundaries, and implemented as a single Symfony-framework Bundle.

This was the root of some disagreements, and of course, like every single
implementation of Open Source, but we noticed that there was an opinion repeated
once and again by some users.

> This project is Framework dependent.

We thought that being a Symfony dependent project was a good idea, but we had to 
decide what to be dependent on. Finally, we decided that Elcodi had to be
coupled to Symfony components and Doctrine library, so we had to split between
simple PHP libraries (Components) and Symfony Framework adaptations of these
libraries (Bundles).

## Component Structure

As we have said before, a component is just a PHP library. Think about it, we could use the Cart
classes in our own project, far from Symfony Framework, and because we don't 
want all the Framework dependencies inside our `vendor/` folder, maybe we should
only require the classes and their dependencies.

This is what a component is. Just classes.

So inside a component you will find a simple categorization of objects. All 
components follow the same structure.

```
Cart
  |
  |- Entity
  |    |- Interfaces
  |    |    |- CartInterface.php
  |    |- Abstracts
  |    |    |- AbstractCart.php
  |    |- Cart.php
  |
  |- Factory
  |    |- CartFactory.php
  |
  |- Repository
  |    |- CartRepository.php
  |
  |- Services
  |    |- CartManager.php
  |
  |- Wrapper
  |    |- CartWrapper.php
  |
  |- Transformer
  |    |- CartOrderTransformer.php
  |
  |- Event
  |    |- OnOrderCreatedEvent.php
  |
  |- EventListener
  |    |- CartEventListener.php
  |
  |- EventDispatcher
  |    |- CartEventDispatcher.php
  |
  |- Controller
  |    |- ImageUploadController.php
  |
  |- Command
       |- SitemapProfileCommand.php
```

This is just a sample of how a Component is structured, and how you should 
structure your project if you want to follow our way of doing things.

> This does not mean that this one is the best way of structuring your code, or
> does not mean that there is not another way of doing it. Is just our
> implementation and the one we suggest you.

## Classes Standards

In order to create homogeneous code, we've defined a set of rules when creating
and naming classes.

### Abstracts

Maybe is not the best standard, but we strongly believe that the name of a class
should explain its mission as much as possible. Because this
is an OpenSource project we must take care about all different skilled 
developers so the name of an abstract class, even if it is redundant, will have
the word `Abstract` at the end of the name.

``` php
abstract class AbstractFactory
{
}
```

Furthermore, any abstract class will be placed in a folder called `Abstracts/`,
to make them easier to find.

``` php
\Elcodi\Component\Core\Factory\Abstracts\AbstractFactory
```

> The namespace must be Abstracts instead of Abstract because Abstract is a PHP
> reserved keyword


### Traits

Same as Abstract classes. All Traits will have the word `Trait` at the end of 
the name

``` php
trait DateTimeTrait
{
}
```

Furthermore, any trait will be placed in a folder called `Traits/`, to make them 
easier to find.

``` php
\Elcodi\Component\Core\Entity\Traits\DateTimeTrait
```

> The namespace must be Traits instead of Trait because Trait is a PHP reserved 
> keyword

There is a limitation in the definition of Traits in PHP, and it is that
there is a collision problem of namespaces. Because they are virtually 
copy/pasted to the final class, you can have some problems if you work with
namespaces inside the Trait classes.

The way of solving this is never using use statements in a Trait, and always 
using all the namespace when referencing a PHP namespace.

``` php
trait PriceTrait
{
    /**
     * @var \Elcodi\Component\Currency\Entity\Interfaces\CurrencyInterface
     *
     * Currency for the amounts stored in this entity
     */
    protected $productCurrency;
```

### Interfaces

Same as Abstract classes. All Interfaces will have the word `Interface` at the 
end of the name

``` php
interface OrderInterface
{
}
```

Furthermore, any interface will be placed in a folder called `Interfaces/`, to 
make them easier to find.

``` php
\Elcodi\Component\Cart\Entity\Interfaces\OrderInterface
```

> The namespace must be Interfaces instead of Interface because Interface is a 
> PHP reserved keyword

### Bundles

Each Bundle should avoid the magic of auto-discovering, placed in the class
`Symfony\Component\HttpKernel\Bundle\Bundle`.

Yes, we're talking about these methods:

* getContainerExtension()
* registerCommands()

In order to forget about magic there, and to clearly define our Bundles, we will
always overwrite these methods, returning specifically the data is intended to
return, even if methods are empty.

``` php
use Symfony\Component\Console\Application;
use Symfony\Component\DependencyInjection\Extension\ExtensionInterface;
use Symfony\Component\HttpKernel\Bundle\Bundle;

use Elcodi\Bundle\CoreBundle\DependencyInjection\ElcodiCoreExtension;

/**
 * ElcodiCoreBundle Bundle
 *
 * This is the core of the suite.
 * All available bundles in this suite could have this bundle as a main
 * dependency.
 */
class ElcodiCoreBundle extends Bundle
{
    /**
     * Returns the bundle's container extension.
     *
     * @return ExtensionInterface The container extension
     */
    public function getContainerExtension()
    {
        return new ElcodiCoreExtension();
    }

    /**
     * Register Commands.
     *
     * Disabled as commands are registered as services
     *
     * @param Application $application An Application instance
     */
    public function registerCommands(Application $application)
    {
        return;
    }
}
```

## Dependency Injection

We care as well on how Elcodi services are defined in the Symfony 
Dependency Injection container, in order to make easier for the final user to
remember the name of the service given the namespace of the class.

First of all what you need to know is that all services in Elcodi ecosystem 
start with `elcodi.`.

For example

``` yaml
elcodi.event_dispatcher.cart
elcodi.manager.cart
```

We have structured all definitions in two, three or four levels.

### Two levels services

Elcodi specific and most important services in Elcodi ecosystem. These services
cannot be grouped by any kind of *namespace* because they are not an 
implementation of any family.

``` yaml
elcodi.languages
elcodi.languages_iso
elcodi.languages_iso_array
elcodi.locale
```

### Three levels services

Most of services will have three levels of definition. Let's analyze previous
examples:

``` yaml
elcodi.event_dispatcher.cart
```

**First level** is always `elcodi`. This will help you identifying all Elcodi's
definition spectre.

**Second level** is the group or the final meaning of such service. For example,
in this example our service will be an EventDispatcher.
We have some examples of different kind of services in our project

``` yaml
elcodi.event_dispatcher.cart
elcodi.event_listener.address_clone_update_carts
elcodi.manager.cart
elcodi.wrapper.cart
elcodi.factory.cart
elcodi.repository.cart
elcodi.object_manager.cart
elcodi.director.cart
elcodi.transformer.cart_order
```

**Third level** is the name of given type. In last example we were creating, for
example, the cart factory, or the cart event dispatcher. This last element has
nothing to do with the name of the component, and must be extremely related with
the content of the class.

### Small classes and methods

Each class should be small enough to contain specific information. Remember to
use small methods (no more than 20 lines) and to take care of their complexity.
The more complex a method is, the harder to understand what is all
about.

### PHPDoc

This is an important part of the project.

All methods must be explained for humans. Some people think that the code should
be self-explanatory, and we agree on that. This doesn't mean that all people 
should read the code to understand what a method is intended for.

Pretending them to understand our method by its code is to prevent them to focus 
on their own business logic, and can hinder development speed.

All methods must follow these rules as well, related to PHP Doc format

``` php
/**
 * This is my detailed description about what my code is intended for
 * I can use as many lines as possible.
 * I repeat, as many lines as possible.
 *
 * @param MyObject $myObject My object description
 * @param string   $info     Some information
 * @param integer  $number   Some number
 *
 * @return Factory Description of the result
 *
 * @throws AnException Scenario when this exception is thrown
 */
```

Using this kind of blocks there are some tips we all need to take into account.

* There is a space between all blocks
* The order is: Description, Params, Return and Exceptions
* All params should be aligned as shown in the example
* Never use @inheritBlock. It is not useful at all and makes the
comprehension of the code so difficult

## Tools

We provide you a way of converting your way of programming to ours. Why don't 
you use these tools?

### PHP-CS-Fixer

* (Github repository)[https://github.com/FriendsOfPHP/PHP-CS-Fixer]

This library analyzes all the PHP source and tries to fix coding standards 
issues, using a custom definition set in the root of the project.

``` php
<?php

return Symfony\CS\Config\Config::create()
    // use SYMFONY_LEVEL:
    ->level(Symfony\CS\FixerInterface::SYMFONY_LEVEL)
    // and extra fixers:
    ->fixers(array(
        'concat_with_spaces',
        'multiline_spaces_before_semicolon',
        'short_array_syntax',
        '-remove_lines_between_uses'
    ))
    ->finder(
        Symfony\CS\Finder\DefaultFinder::create()
            ->in('src/')
    )
;
```

This library is a dependency of the project, but only in development 
environment, so while you are developing, if you have already updated your 
composer dependencies, you can run the fixer as follows

``` bash
php bin/php-cs-fixer fix
```

Because the dependency is set using an specific version of the code, all 
developers will fix the code the same way.

### PHP-Formatter

* (Github repository)[https://github.com/mmoreram/php-formatter]

Another simple php formatter. This one will just format your class headers, 
setting the project headers in your PHP classes, and will sort all use statements in
order to comply with a given specification.

This library is a dependency of the project, but only in development 
environment, so while you are developing, if you have already updated your 
composer dependencies, you can run the fixer as follows:

``` bash
php bin/php-formatter formatter:use:sort src/
php bin/php-formatter formatter:header:fix src/
```

Because the dependency is set using an specific version of the code, all 
developers will fix the code the same way.
