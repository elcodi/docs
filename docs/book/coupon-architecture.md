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

Some factory related links

* [How to implement a factory](../cookbook/implementation/implement-a-factory.md)

## Coupon types

There are two type of coupons available in Elcodi, the amout coupons and the
percent coupons.

``` php
/**
 * Class ElcodiCouponTypes
 */
final class ElcodiCouponTypes
{
    /**
     * @var integer
     *
     * Coupon type absolute amount
     */
    const TYPE_AMOUNT = 1;
    
    /**
     * @var integer
     *
     * Coupon type percent
     */
    const TYPE_PERCENT = 2;
}
```

The Coupon class has a private property called `$type` and can be filled using
always static variables of that final class.

``` php
use Elcodi\Component\Coupon\Entity\Coupon;
use Elcodi\Component\Coupon\ElcodiCouponTypes;

$coupon = new Coupon();
$coupon->setType(ElcodiCouponTypes::TYPE_AMOUNT);
```

If you use the default Coupon factory, then any new coupon uses type
`TYPE_AMOUNT` by default.

When the coupon type is the amount one, you can use the method `setPrice()` to
define the total value of the coupon, while if the coupon type is the percent
one, then you must use the method `setDiscount` in order to allow the system
calculate the final value of the Cart.

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
`$minimumPurchase`. This property is a Money.

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
null, then this rules is applied. Of course, if the rule execution result is
false, the coupon is refused.

you can read some information about rules by following these links

* [New in Symfony 2.4: The ExpressionLanguage Component](http://symfony.com/blog/new-in-symfony-2-4-the-expressionlanguage-component)
* [ExpressionLanguage Symfony Component](http://symfony.com/doc/current/components/expression_language/index.html)