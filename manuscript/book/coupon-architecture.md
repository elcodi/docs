# Coupon Architecture

Our coupon architecture is intended to work as a simple coupon server. All the
coupon model is decoupled from every other package, so you should be able to
work with coupons, and only with coupons.

In this chapter we will take a look at the different types of coupons we can
work in Elcodi. Of course, this model can evolve and get better.

## Create coupon

If you want to create a new coupon instance, you should always use the factory
object defined in the dependency injection layer. The service is defined with
the name `elcodi.factory.coupon` and is available for injection as follows.

``` yaml
services:
    my_service:
        class: My\Service\Namespace
        arguments:
            - @elcodi.factory.coupon
```

a small snippet of this service could be that one

``` php
$coupon = $this
    ->couponFactory
    ->create()
```

Please, follow this cookbook if you need more information about elcodi factories.

* [How to implement a factory](http://elcodi.io/docs/cookbook/implementation/implement-a-factory/)

## Coupon types

> Important. This part of the documentation describes how Elcodi manages the
> types involving the elcodi's Cart implementation.

There are several types of application in elcodi Coupons and each of them uses
a certain part of the Coupon entity. The type of the coupon can be defined using
the field `$type` of Coupon.

``` php
use Elcodi\Component\Coupon\Entity\Coupon;

$coupon = new Coupon();
$coupon->setType($type);
```

Let's take a look to each one.

### Absolute value coupons

This is the common and default coupon type. You apply an specific discount for
the cart defined by an specific amount, for example 10 euros.

``` php
use Elcodi\Component\CartCoupon\Applicator\AbsoluteCartCouponApplicator;

$price = ... // Money instance

$coupon->setType(AbsoluteCartCouponApplicator::id();
$coupon->setPrice($price);
```

### Percent coupons

This coupon introduces a percent discount for the final cart.

``` php
use Elcodi\Component\CartCoupon\Applicator\PercentCartCouponApplicator;

$coupon->setType(PercentCartCouponApplicator::id();
$coupon->setDiscount(15);
```

### NxM coupons

This coupon is intended to be a *"buy n units and pay only m"*. Because the
system should always be opened for evolution, the configuration of this coupon
is done by using an specific syntax.

``` php
use Elcodi\Component\CartCoupon\Applicator\Abstracts\AbstractMxNCartCouponApplicator;

$coupon->setType(AbstractMxNCartCouponApplicator::id();
$coupon->setValue('xxx');
```

In this chapter we will explain how to configure these coupons. This
configuration is splitted in three blocks. Each one defines an specific part of
the coupon and they are splitted by the symbol `:`.

#### NxM Configuration

First block. Configures the NxM definition:

* N: Units to buy
* M: Units to pay

For example, 3x2 means pay 2 and buy 3. That simple.

#### Filtering Configuration

The second block aims to be the filter of the coupon. What elements should this
coupon be applied on? You can filter by using product ids, category ids or
manufacturer ids.

Let's see some examples with the explanation of the meaning (all of them start
with Pay 2 and Buy 3):

* **3x2:p(12)** - Only applies to purchasables with id 12
* **3x2:p(50,2)** - Only applies to purchasables with id 50 or 2
* **3x2:c(1)** - Only applies to purchasables of category with id 1
* **3x2:c(1,3)** - Only applies to purchasables of category with id 1 or 3
* **3x2:m(5)** - Only applies to purchasables of manufacturer with id 5
* **3x2:m(5,45)** - Only applies to purchasables of manufacturer with id 5 or 45
* **3x2:c(50)&m(2)** - Only applies to purchasables of category with id 50 and
manufacturer with id 2
* **3x2:p(10)|m(2)|c(4,10)** - Only applies to purchasables that have id 50, or
with category 4 or 10, or with manufacturer 2.

#### Modifiers Configuration

Last block. Once you've filtered, you can configure some special behaviors of
your coupon. Each one of the modifiers are a simple letter at the end of the
expression. You can add as many as you need, no matter the order between them.

The first group of modifiers are related to what family of Purchasables this
coupon should apply with.

* P - Coupon applicable only on Products
* V - Coupon applicable only on Variants
* K - Coupon applicable only on Packs

You can combine them all

* PK - Coupon applicable only on Products and Packs

Lets see an example

* **3x2:c(12):PK** - Pay 2 and buy 3. Applicable only on products and packs that
are from category with id 12.

The second group of modifiers are related to how to apply the coupon once all
purchasable elements are selected.

By default, your MxN coupon is applied to each purchasable selected. For
example, if your cart has a product 3 times and a pack 3 times, both available
for a 3x2 coupon, the coupon will be applied for each one in an independent way,
so you will pay 2 products and 2 packs. One of each them will be free.

* G - Group modificator. This modificator allow you to treat all purchasables as
the same when applying the coupon. With the last example, you would have 6
purchasables, and you would get free only the 2 most cheap (no matter if they
are products or packs).

**3x2:c(12):PKG**

### Adding a new filter

You can add a new filter by creating a new implementation of interface
`Symfony\Component\ExpressionLanguage\ExpressionLanguage`.

This filter uses the ExpressionLanguage that Symfony provides, so make sure you
know how it works.

This interface requires you to implement a method called registerFunction.

``` php
/**
 * Interface ExpressionLanguageFunctionInterface.
 */
interface ExpressionLanguageFunctionInterface
{
    /**
     * Register function.
     *
     * @param ExpressionLanguage $expressionLanguage Expression language
     */
    public function registerFunction(ExpressionLanguage $expressionLanguage);
}
```

Once you have created this implementation, you need to register it in your
dependency injection file with a tag.

``` yml
services:
    elcodi.cart_coupon_applicator_function.custom:
        class: My\Custom\Applicator\Function\Namespace
        public: false
        tags:
            - { name: elcodi.cart_coupon_applicator_function }
```

### Adding a new Type

You can add a new Type as well. In that case you must create a new
implementation for interface
`Elcodi\Component\CartCoupon\Applicator\Interfaces\CartCouponApplicatorInterface`.

This interface requires you to implement these methods.

``` php
/**
 * Interface CartCouponApplicatorInterface.
 */
interface CartCouponApplicatorInterface
{
    /**
     * Get the id of the Applicator.
     *
     * @return string Applicator id
     */
    public static function id();

    /**
     * Can be applied.
     *
     * @param CartInterface   $cart   Cart
     * @param CouponInterface $coupon Coupon
     *
     * @return bool Can be applied
     */
    public function canBeApplied(
        CartInterface $cart,
        CouponInterface $coupon
    );

    /**
     * Calculate coupon absolute value.
     *
     * @param CartInterface   $cart   Cart
     * @param CouponInterface $coupon Coupon
     *
     * @return MoneyInterface|false Absolute value for this coupon in this cart.
     */
    public function getCouponAbsoluteValue(
        CartInterface $cart,
        CouponInterface $coupon
    );
}
```

Once you have implemented this method, then you need to register your new
service in your dependency injection configuration file.

``` yml
services:
    elcodi.cart_coupon_applicator.custom:
        class: My\Custom\Applicator\Namespace
        tags:
            - { name: elcodi.cart_coupon_applicator }
```

## Coupon enforcement

Another possible coupon configuration is the enforcement. The real meaning of
that value is for *How the coupon is applied*. Values can be manually and
automatically.

``` php
namespace Elcodi\Component\Coupon;
/**
 * Class ElcodiCouponTypes
 */
final class ElcodiCouponTypes
{
    /**
     * @var integer
     *
     * Automatic enforcement
     */
    const ENFORCEMENT_AUTOMATIC = 1;
    
    /**
     * @var integer
     *
     * Manual enforcement
     */
    const ENFORCEMENT_MANUAL = 2;
}
```

With the first one you are creating a coupon that will be applied automatically
in all carts. This doesn't mean that will be really applied to all of them, but
just tried.

## Minimum purchase

You can define that a coupon is only applicable when a cart price is above a 
certain price. The way to do that is by using the property called
`$minimumPurchase`. This property may be an instance of `Elcodi\Component\Currency\Entity\Money`.

``` php
/**
 * Set minimum purchase
 *
 * @param MoneyInterface $amount Absolute Price
 *
 * @return $this Self object
 */
public function setMinimumPurchase(MoneyInterface $amount)
{
    $this->minimumPurchaseAmount = $amount->getAmount();
    $this->minimumPurchaseCurrency = $amount->getCurrency();
    return $this;
}
/**
 * Get minimum purchase
 *
 * @return MoneyInterface Absolute Price
 */
public function getMinimumPurchase()
{
    return Money::create(
        $this->minimumPurchaseAmount,
        $this->minimumPurchaseCurrency
    );
}
```

## Adding some Rules

When a Coupon is intended to be applied in a Cart and the field `$rule` is not
null, then this rule is applied. Of course, if the rule execution result is
false, the coupon is refused.

you can read some information about rules by following these links

* [New in Symfony 2.4: The ExpressionLanguage Component](http://symfony.com/blog/new-in-symfony-2-4-the-expressionlanguage-component)
* [ExpressionLanguage Symfony Component](http://symfony.com/doc/current/components/expression_language/index.html)
