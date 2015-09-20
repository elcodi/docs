# Payments

All e-commerce, for sure, need to have a payment platform for completing the
shopping workflow successfully.

For this integration or implementation, all kind of projects are used to working
with external PHP libraries, so in our case we are not going to be different at
all (and because we believe in third party projects as well). In this example we
will work using a simple and free payment example, so the real meaning of these
lines is showing you how to build your own payment library.

## Requirements

This chapter is a little bit complex, so let's focus on some interesting topics
related with Payments and this implementation. Some extra information will be
enough to have a base before starting this read.

* [Plugins](plugins.md)
* [Dependency Injection Symfony Component](http://symfony.com/doc/current/components/dependency_injection/introduction.html)
* [Event Dispatcher Symfony Component](http://symfony.com/doc/current/components/event_dispatcher/introduction.html)
* [PaymentSuite Payment PHP Library](http://github.com/paymentsuite)
* [Payum Payment PHP](http://github.com/Payum)

## Example

Let's figure out that we want to add a new free payment module. This payment
method will have a reference name called `free_payment`, and even is not
important at all what's the name, we will use it to detect how we should use the
name, whatever it is, in our services and bundle implementation.

Our payment method will be always available.

## Calling for Shipping methods

Imagine that your cart is already completed and finished. Then it's time to pay
it. Elcodi platform only provides you one single and easy way of collecting all
these methods.

It is important to understand that Elcodi will never consider throwing any kind
of event related to any specific payment method neither any order paid related
one, so each integration should have their own events for that.

In other words, Elcodi only will provide a simple point of entry to the payment
engine.

At the point we need to  want to collect all payment methods, we are going to
use the service created specifically for that.

``` php
$paymentMethods = $this
    ->get('elcodi.wrapper.payment_methods')
    ->get();
```

Internally, this piece of code dispatches an event called 
*["payment.collect"](events.md#payment.collect)*, responsible exclusively for
collecting all these payment methods.

It is necessary to say that at this point, the Event Dispatcher component is not
working in a very strict way it should work, so it's being used as a collector
manager instead of an event dispatcher. The difference between both is that an
event should be inmutable (an event just happends, and there's no much to be
done then... just add some reactions using event listeners), but in that case,
the object Event has a `hasXXX` method, so, even not being imortant the order
of the listeners execution, the event is being modified on the fly. A changer
event... weird, right?

* [Implement a collector](../cookbook/implementation/implement-a-collector.md)

Anyway, the recollection event is dispatched and all installed and initialized
Plugins (in Kernel) are called to add some payment methods there.

## Adding new Payment Method

For you, developer, that you want to add a payment method, that's your chapter.
Remember the event *["shipping.collect"](events.md#shipping.collect)*? Well, we
must listen it in order to add new payment methods.

Since now, none object is passed though the event in order to decide if a
payment method is available or not, so only the plugin statement should be
checked.

Let's take a look firstly how can we subscribe to this event using the same
methodology of subscribing any event.

``` yml
services:

    #
    # Event Listeners
    #
    elcodi_plugin.free_payment.event_listener.payment_collect:
        class: Elcodi\Plugin\FreePayment\EventListener\PaymentCollectEventListener
        tags:
            - { name: kernel.event_listener, event: payment.collect, method: addCustomPaymentMethods }
```

Great. In this Event Listener we should add all our business logic (of course,
is much better to place all your business logic inside a service, but it depends
on your strategy). We'll doing like this in this example. Let's take a look.

``` php
use Elcodi\Component\Currency\Entity\Money;
use Elcodi\Component\Payment\Entity\PaymentMethod;
use Elcodi\Component\Payment\Event\PaymentCollectionEvent;

/**
 * Class PaymentCollectEventListener
 */
class PaymentCollectEventListener
{
    /**
     * Look for available payment methods
     *
     * @param PaymentCollectionEvent $event Event
     *
     * @return $this Self object
     */
    public function addCustomPaymentMethods(PaymentCollectionEvent $event)
    {
        $event
            ->addPaymentMethod(new PaymentMethod(
                'free-payment',
                'Your order for free!',
                'You can buy whatever you want for free!',
                'http://my-free-payment.com',
                'http://my-free-payment.com/logo.png,
                '<script></script>'
            ));
    }
}
```

As you can see, when we create a new instance of the object `PaymentMethod` you
can add some properties like `url`, `imageUrl` and `script`.

``` php
namespace Elcodi\Component\Payment\Entity;

/**
 * Class PaymentMethod
 */
class PaymentMethod
{
    /**
     * Construct
     *
     * @param string $id          Id
     * @param string $name        Name
     * @param string $description Description
     * @param string $url         Url
     * @param string $imageUrl    Image url
     * @param string $script      Script
     */
    public function __construct(
        $id,
        $name,
        $description,
        $url,
        $imageUrl = '',
        $script = ''
    ) {
        $this->id = $id;
        $this->name = $name;
        $this->description = $description;
        $this->url = $url;
        $this->imageUrl = $imageUrl;
        $this->script = $script;
    }
}
```

This is because, in most cases, a payment platform, or in all middleware
integrations, we are used to working with one of these scenarios:

* The platform proposes us an external url with an associated image. It is
common to see this when our user must jump to another domain for the payment
action. In that case, we should use both `url` and `imageUrl`.
* The platform provides us an script with HTML and some JS. This is used for
internal payment with some callable urls managed by the script itself. In that
case you should use `script` and `imageUrl`.

## Payment scripts

To work with platforms that require some HTML or JS to be used, then it is
common and healthy to use Twig templating, instead of PHP variables.

Let's see a little bit more complex example of how to use speciic scripts and
the router for the creation of routes.

``` php
use Symfony\Bundle\FrameworkBundle\Templating\EngineInterface;
use Symfony\Component\Form\FormFactory;
use Symfony\Component\Routing\Generator\UrlGeneratorInterface;

use Elcodi\Component\Currency\Entity\Money;
use Elcodi\Component\Payment\Entity\PaymentMethod;
use Elcodi\Component\Payment\Event\PaymentCollectionEvent;

/**
 * Class PaymentCollectEventListener
 */
class PaymentCollectEventListener
{   
    /**
     * @var EngineInterface
     *
     * Twig engine
     */
    protected $templating;
    
    /**
     * @var FormFactory
     *
     * Form factory
     */
    protected $formFactory;
    
    /**
     * @var UrlGeneratorInterface
     *
     * Router
     */
    protected $router;
    
    /**
     * Construct
     *
     * @param EngineInterface $templating         Twig engine
     * @param FormFactory     $formFactory        Form factory
     * @param UrlGeneratorInterface $urlGenerator Url generator
     */
    public function __construct(
        EngineInterface $templating,
        FormFactory $formFactory,
        UrlGeneratorInterface $urlGenerator
    ) {
        $this->templating = $templating;
        $this->formFactory = $formFactory;
        $this->urlGenerator = $urlGenerator;
    }

    /**
     * Look for available payment methods
     *
     * @param PaymentCollectionEvent $event Event
     *
     * @return $this Self object
     */
    public function addCustomPaymentMethods(PaymentCollectionEvent $event)
    {
        /**
         * If we need to create a new url
         */
        $url = $this
            ->urlGenerator
            ->generate('free_payment_url);
        
        /**
         * If we need to inject a custom view with a custom form view, then
         * you must create both elements like is shown here
         */
        $form = $this
            ->formFactory
            ->create('free_payment_form_type')
            ->createView();
            
        $view = $this
            ->templating
            ->render('FreePaymentBundle:Payment:view.html.twig', [
                'form' => $form,
            ]);
    
        $event
            ->addPaymentMethod(new PaymentMethod(
                'free-payment',
                'Your order for free!',
                'You can buy whatever you want for free!',
                $url,
                'http://my-free-payment.com/logo.png,
                $view
            ));
    }
}
```

And the Dependency Injection definition would be like this

``` yml
services:

    #
    # Event Listeners
    #
    elcodi_plugin.free_payment.event_listener.payment_collect:
        class: Elcodi\Plugin\FreePayment\EventListener\PaymentCollectEventListener
        arguments:
            - @templating
            - @form.factory
            - @router
        tags:
            - { name: kernel.event_listener, event: payment.collect, method: addCustomPaymentMethods }
```

## Accessing collected methods

Once we have added all our payment methods, then we should be able to access all
the information in order to be able to offer all payment methods to the final
user.

This is a piece of code of our template.

``` jinja
<div class="form-checkout">
    {% for paymentMethod in paymentMethods %}
        {% set paymentMethodName = paymentMethod.name %}
        {% if paymentMethod.url %}
                <h2>{{ paymentMethodName|trans }}</h2>
                <p><a href="{{ paymentMethod.url }}" class="button button-secondary">{{ 'template.store_template_bundle.checkout.payment.continue'|trans }}</a></p>
                <hr />
        {% else %}
            <details class="test-payment-{{ paymentMethod.id }} form form-{{ paymentMethodName|trans|lower }}">
                <summary>{{ paymentMethodName|trans }}</summary>
                <div class="payment-wrapper">
                    {{ paymentMethod.script|raw }}
                </div>
            </details>
        {% endif %}

    {% endfor %}
</div>
```

As you can see in both cases (with and without script), each element of the
array named `paymentMethods` is a payment method instance inserted by each
Payment integration plugin previously.

Another way to access the payment methods directly from the templating layer is
using the Twig function.

``` jinja
{% set paymentMethods = elcodi_payment_methods() %}
```

## Payment

As said before, each platform should take care about the payment process. Of 
course, only one strict action must be done in this process, and this is the
transformation of the cart to a valid order.

This action is not responsibility of the payment platform at all, but it should
dispatch the change by using one available service.

``` php
$cart = $this
    ->cartWrapper
    ->get();
    
$order = $this
    ->container
    ->get('elcodi.transformer.cart_order')
    ->createOrderFromCart($cart);
```

## Payment State Machine

At this point, the order is ready to be shipped. Once starting these last steps
on this documentation, please take a look at the State Transition Machine 
documentation.

* [State Transition Machine](../component/state-transition-machine.md)

Bamboo. by default, proposes a payment state diagram like we can see here.

| From    | Action | To       |
|---------|--------|----------|
| unpaid  | pay    | paid     |
| paid    | refund | refunded |

and through the Symfony Container we have access to some specific objects of the
Payment State Machine.

``` yaml
#
# Order state machine for Shipping
#
elcodi.order_payment_states_machine_builder:
    class: Elcodi\Component\StateTransitionMachine\Machine\MachineBuilder
    arguments:
        - @elcodi.factory.state_transition_machine
        - %elcodi.order_payment_states_machine_identifier%
        - %elcodi.order_payment_states_machine_states%
        - %elcodi.order_payment_states_machine_point_of_entry%

elcodi.order.payment_states_machine:
    class: Elcodi\Component\StateTransitionMachine\Machine\Machine
    factory:
        - @elcodi.order_payment_states_machine_builder
        - compile

elcodi.order_payment_states_machine_manager:
    class: Elcodi\Component\StateTransitionMachine\Machine\MachineManager
    arguments:
        - @elcodi.order.payment_states_machine
        - @event_dispatcher
        - @elcodi.factory.state_transition_machine_state_line
```

## Updating the Payment state

With this access to the Payment State Machine, we can change the state of an
Order by using the service `elcodi.order_payment_states_machine`.

Let's see an example of how to go to the state called `paid`, taking in account
that our actual state is called `unpaid`.

``` php
$order = // Order instance;

$stateLineStack = $this
    ->get('elcodi.order_payment_states_machine_manager')
    ->transition(
        $order,
        $order->getPaymentStateLineStack(),
        'paid',
        'Order paid by credit cart'
    );
    
$order->setPaymentStateLineStack($stateLineStack);
$this
    ->get('elcodi.object_manager.order')
    ->flush($order);
```

Only if given transition is possible all is going to work properly. Otherwise
some Exceptions will be thrown.
