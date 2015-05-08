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