Classes Standards
=================

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