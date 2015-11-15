# Elcodi Events

Full Elcodi Events documentation. Each entry describes what the event is 
created for, where is thrown and how can be used.

### Priorities

Each time an event is dispatched, a bunch of subscribers are ready to be 
executed. But who says which one must be executed first, and which one must be
executed the last one?

Well, each event listener can have a priority value, and once all these 
listeners must be executed, one by one, they are previously sorted by this 
value, from 128 to -127. The more high, the before is executed.

``` yaml
services:
    
    elcodi.event_listener.some:
        class: My\Bundle\EventListener\SomeEventListener
        tags:
            - { name: kernel.event_listener, event: order.oncreated, method: some, priority: 16 }
```

This parameter is not required, and by default is 0. When several listeners 
have the same priority, then the *first found, first executed* solution is 
wisely applied.

### cart.preload

This event is dispatched by the `CartManager` and is intended for one single 
reason: check anything related to a Cart before loading it. Once the cart is 
fetched from the database, and because most of the values must be computed on 
real time (stock must be checked, for example), must be loaded somehow. Loading 
a cart means checking its correctness and computing all prices.

Well, if you need to do some checks after this computation is done, then use 
this event.

``` php
use Elcodi\Component\Cart\Event\CartPreLoadEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartPreLoadEvent $event)
{
    $cart = $event->getCart();
}
```

### cart.onload

Once the cart has been checked, this one can be loaded. This event is thrown 
only when the cart is valid and, after it, cart instance must be complete and
ready to be used by the application.

``` php
use Elcodi\Component\Cart\Event\CartOnLoadEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartOnLoadEvent $event)
{
    $cart = $event->getCart();
}
```

### cart.onempty

You can subscribe as well when the cart is emptied, so you can add some checks
or actions related to this action.

``` php
use Elcodi\Component\Cart\Event\CartOnEmptyEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartOnEmptyEvent $event)
{
    $cart = $event->getCart();
}
```

### cart.inconsistent

Once the cart is being checked, one of the possibilities is that any cart line
is invalid, for example because there is no stock anymore of the product 
related, or because the product has been disabled meanwhile.

By default, these lines are removed from the cart, but you can add some extra
behavior by subscribing to this event.

``` php
use Elcodi\Component\Cart\Event\CartInconsistentEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartInconsistentEvent $event)
{
    $cart = $event->getCart();
    $cartLine = $event->getCartLine();
}
```

### cart_line.onadd

Each time a new cart line is created and added into a cart, this event is 
dispatched.

``` php
use Elcodi\Component\Cart\Event\CartLineOnAddEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartLineOnAddEvent $event)
{
    $cart = $event->getCart();
    $cartLine = $event->getCartLine();
}
```

### cart_line.onedit

Each time an existing cart line is edited, this event is dispatched. To edit a
cart line means changing its quantity, for example.

``` php
use Elcodi\Component\Cart\Event\CartLineOnEditEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartLineOnEditEvent $event)
{
    $cart = $event->getCart();
    $cartLine = $event->getCartLine();
}
```

### cart_line.onremove

Each time an existing cart line is removed, this event is dispatched.

``` php
use Elcodi\Component\Cart\Event\CartLineOnEditEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartLineOnEditEvent $event)
{
    $cart = $event->getCart();
    $cartLine = $event->getCartLine();
}
```

### order.precreated

Once a cart is purchased, a new Order instance must be created from it. This 
event is dispatched before this order is created, so the instance is not a 
reality yet.

Indeed, this event is more related to the cart that is going to be converted, so
any business logic related to "This cart is going to be transformed into an 
Order", should be placed here, no matter how the Order is.

``` php
use Elcodi\Component\Cart\Event\OrderPreCreatedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(OrderPreCreatedEvent $event)
{
    $cart = $event->getCart();
}
```

### order.oncreated

The Order is now being created, so any kind of business logic related to the 
Cart that is being transformed, or the new Order, you can subscribe to this
event.

``` php
use Elcodi\Component\Cart\Event\OrderOnCreatedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(OrderOnCreatedEvent $event)
{
    $cart = $event->getCart();
    $order = $event->getOrder();
}
```

### order_line.oncreated

When creating the order given a cart, for each cart line found, a new order line
is created as well. Each time this happens, a new event of this type is 
dispatched.

This is used, for example, when the stock must be updated from each cart line.

``` php
use Elcodi\Component\Cart\Event\OrderLineOnCreatedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(OrderLineOnCreatedEvent $event)
{
    $cart = $event->getCart();
    $order = $event->getOrder();
    $orderLine = $event->getOrderLine();
}
```

### cart_coupon.oncheck

Each time a cart is loaded, all coupons are checked one by one. You can access
to the checked coupon and to the full cart object.

``` php
use Elcodi\Component\CartCoupon\Event\CartCouponOnCheckEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartCouponOnCheckEvent $event)
{
    $cart = $event->getCart();
    $coupon = $event->getCoupon();
    $cartCoupon = $event->getCartCoupon();
}
```

### cart_coupon.onapply

Each time a new coupon is applied to an existing cart, this event is dispatched.

The association itself is done by an Event Listener as well with priority 0, so
before that, the association object will not be available.

``` php
use Elcodi\Component\CartCoupon\Event\CartCouponOnApplyEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartCouponOnApplyEvent $event)
{
    $cart = $event->getCart();
    $coupon = $event->getCoupon();
    $cartCoupon = $event->getCartCoupon();
}
```

### cart_coupon.onremove

Each time a coupon is removed from a cart, this event is dispatched. 

Take in account that the coupon is really removed from the cart (only the 
association is removed, not the coupon itself) by an Event Listener with 
priority 0.

``` php
use Elcodi\Component\CartCoupon\Event\CartCouponOnRemoveEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartCouponOnRemoveEvent $event)
{
    $cart = $event->getCart();
    $coupon = $event->getCoupon();
    $cartCoupon = $event->getCartCoupon();
}
```

### cart_coupon.onrejected

Each time a coupon is intended to be applied to a cart, some kind of checks are
performed in order to know if this coupon is really applicable. If it is, great
then, otherwise, this event is dispatched.

One useful case for that event is notifying the administrator how many times a
coupon has been rejected by the application.

``` php
use Elcodi\Component\CartCoupon\Event\CartCouponOnRejectedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartCouponOnRejectedEvent $event)
{
    $cart = $event->getCart();
    $coupon = $event->getCoupon();
}
```

### order_coupon.onapply

Once the cart is processed and a new order is created from it, for each 
available coupon used and accepted a new association between the coupon and the
order is created. Then, this event is dispatched.

The association itself is done by an Event Listener as well with priority 0, so
before that, the association object will not be available.

``` php
use Elcodi\Component\CartCoupon\Event\CartCouponOnRejectedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CartCouponOnRejectedEvent $event)
{
    $order = $event->getCart();
    $coupon = $event->getCoupon();
    $orderCoupon = $event->getOrderCoupon();
}
```

### comment.onadd

A new comment has been added.

``` php
use Elcodi\Component\Comment\Event\CommentOnAddEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CommentOnAddEvent $event)
{
    $comment = $event->getComment();
}
```

### comment.onedit

An existing comment is being edited.

``` php
use Elcodi\Component\Comment\Event\CommentOnEditEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CommentOnEditEvent $event)
{
    $comment = $event->getComment();
}
```

### comment.preremove
### comment.onremove
### comment.onvoted

### coupon.onused

Each time a certain coupon is used, this coupon is dispatched. Commonly used to
increase the coupon usages.

``` php
use Elcodi\Component\Coupon\Event\CouponOnUsedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(CouponOnUsedEvent $event)
{
    $order = $event->getCart();
    $coupon = $event->getCoupon();
    $orderCoupon = $event->getOrderCoupon();
}
```

### address.onclone

Because an address can be changed after has been used in an order, for example,
each time an address is edited, should be cloned. When this happens, this event
is dispatched in order to some extra actions.

``` php
use Elcodi\Component\Geo\Event\AddressOnCloneEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(AddressOnCloneEvent $event)
{
    $originalAddress = $event->getOriginalAddress();
    $clonedAddress = $event->getClonedAddress();
}
```

### image.preupload

An image is going to be uploaded and saved. Useful for some image manipulation
before being uploaded.

``` php
use Elcodi\Component\Media\Event\ImageUploadedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(ImageUploadedEvent $event)
{
    $image = $event->getImage();
}
```

### image.onupload

Image has been uploaded and saved into database

``` php
use Elcodi\Component\Media\Event\ImageUploadedEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(ImageUploadedEvent $event)
{
    $image = $event->getImage();
}
```

### newsletter.onsubscribe

A user has been subscribed to the newsletter.

``` php
use Elcodi\Component\Newsletter\Event\NewsletterSubscriptionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(NewsletterSubscriptionEvent $event)
{
    $newsletterSubscription = $event->getNewsletterSubscription();
}
```

### newsletter.onunsubscribe

A user has been unsubscribed from the newsletter.

``` php
use Elcodi\Component\Newsletter\Event\NewsletterUnsubscriptionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(NewsletterUnsubscriptionEvent $event)
{
    $newsletterSubscription = $event->getNewsletterSubscription();
}
```

### payment.collect

This event is used by the core when all available payment methods are collected.
Each bundle will inject itself as a payment method.

This event is not passive, but active. This means that is not just an event, but
a collector (implemented the same way in Symfony, but theoretically different).

``` php
use Elcodi\Component\Payment\Entity\PaymentMethod;
use Elcodi\Component\Payment\Event\PaymentCollectionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(PaymentCollectionEvent $event)
{
    $cart = $event->getCart();

    /**
     * Only available with Carts that ...
     */
    if ($cart->getPrice() < //) {

        return;
    }

    $paymentMethod = new PaymentMethod(
        'my-payment-method-24872378942',
        'payment name',
        'payment description',
        $this
            ->router
            ->generate('payment_route')
    );
    
    $event->addPaymentMethod($paymentMethod);
}
```

### shipping.collect

This event is used by the core when all available shipping methods are 
collected. Of course, each shipping method should check if is valid given the
cart, the user information or any other related parameter.

This event is not passive, but active. This means that is not just an event, but
a collector (implemented the same way in Symfony, but theoretically different).

``` php
use Elcodi\Component\Shipping\Entity\ShippingMethod;
use Elcodi\Component\Shipping\Event\ShippingCollectionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(ShippingCollectionEvent $event)
{
    $euro = // Currency instance
    $cart = $event->getCart();
    
    /**
     * Only available with Carts that ...
     */
    if ($cart->getPrice() < //) {
     
        return;
    }
    
    
    $shippingMethod = new ShippingMethod(
        'custom-shipping-method-437829',
        'UPS',
        'Super fast shipping!',
        '4 days shipping, worldwide',
        Money::create(10, $euro);
    );
    
    $event->addShippingMethod($paymentMethod);
}
```

### state_machine.{machine_id}.initialization

Given a machine id, and once this initialized for any transition, this event is
dispatched. Useful for machine configuration.

``` php
use Elcodi\Component\StateTransitionMachine\Event\InitializationEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(InitializationEvent $event)
{
    $object = $event->getObject();
    $stateLineStack = $event->getStateLineStack();
}
```

### state_machine.{machine_id}.transition_from_{state_name}

State transition done by a machine with id {machine_id} that goes from a state
called {state_name} to any state

``` php
use Elcodi\Component\StateTransitionMachine\Event\TransitionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(TransitionEvent $event)
{
    $object = $event->getObject();
    $stateLineStack = $event->getStateLineStack();
    $transition = $event->getTransition();
}
```

### state_machine.{machine_id}.transition_{transition_name}

State transition with name {transition_name} done by a machine with id 
{machine_id}.

``` php
use Elcodi\Component\StateTransitionMachine\Event\TransitionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(TransitionEvent $event)
{
    $object = $event->getObject();
    $stateLineStack = $event->getStateLineStack();
    $transition = $event->getTransition();
}
```

### state_machine.{machine_id}.transition_to_{state_name}

State transition done by a machine with id {machine_id} that goes from any state
to a state called {state_name}

``` php
use Elcodi\Component\StateTransitionMachine\Event\TransitionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(TransitionEvent $event)
{
    $object = $event->getObject();
    $stateLineStack = $event->getStateLineStack();
    $transition = $event->getTransition();
}
```

### state_machine.transition

Any state transition done by any state machine

``` php
use Elcodi\Component\StateTransitionMachine\Event\TransitionEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(TransitionEvent $event)
{
    $object = $event->getObject();
    $stateLineStack = $event->getStateLineStack();
    $transition = $event->getTransition();
}
```

### password.remember

Some user have used the password remember feature. This means that a new url has
been created for that user in order to give her the possibility to create a new
password in a very secured way.

``` php
use Elcodi\Component\User\Event\PasswordRememberEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(PasswordRememberEvent $event)
{
    $user = $event->getUser();
    $rememberUrl = $event->getRememberUrl();
}
```

### password.recover

A user has changed the password by using the remember url. Once this password
changes, this event is dispatched.

``` php
use Elcodi\Component\User\Event\PasswordRecoverEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(PasswordRecoverEvent $event)
{
    $user = $event->getUser();
}
```

### user.register

A new user object has been registered. Many type of users can coexist, but all
will dispatch the same event. You can check the type of user inside your service
by using `instanceof`.

``` php
use Elcodi\Component\User\Event\UserRegisterEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(UserRegisterEvent $event)
{
    $user = $event->getUser();
    
    if ($user instanceof CustomerInterface) {
    
        // ...
    } else {
        // ...
    }
}
```

### customer.register

A new customer object has been registered. Use this event if you want to listen
only when a customer is registered, instead of any kind of user.

``` php
use Elcodi\Component\User\Event\UserRegisterEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(UserRegisterEvent $event)
{
    $customer = $event->getUser();
}
```

### adminuser.register

A new adminuser object has been registered. Use this event if you want to listen
only when a admin user is registered, instead of any kind of user.

``` php
use Elcodi\Component\User\Event\UserRegisterEvent;

/**
 * Method subscribed to the event
 */
public function doSomething(UserRegisterEvent $event)
{
    $customer = $event->getUser();
}
```