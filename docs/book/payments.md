# Payments

Todo E-commerce, claro, debe tener una plataforma de pago para que los clientes
puedan terminar satisfactoriamente su proceso de compra.

Normalmente, para dicha implementación se tienen en cuenta librerías externas de
PHP, por lo que en este caso vamos a trabajar con un simple sistema de pago, en
este caso gratuito, dado que solo lo vamos a utilizar para ver como Elcodi
propone sus servicios y eventos para recolectar y utilizar los sistemas de pago
disponibles en todo momento.

## Requisites

This chapter is a little bit complex, so let's focus on some interesting topics
related with Payments and this implementation. Some extra information will be
enough to have a base before starting this read.

* [Plugins](plugins.md)
* [Dependency Injection Symfony Component](http://symfony.com/doc/current/components/dependency_injection/introduction.html)
* [Event Dispatcher Symfony Component](http://symfony.com/doc/current/components/event_dispatcher/introduction.html)
* [PaymentSuite Payment PHP Library](http://github.com/paymentsuite)
* [Payum Payment PHP](http://github.com/Payum)

## Example

Imaginemos que queremos añadir un sistema de pago gratuito. Dicho sistema de 
pago tiene como nombre de referencia `free_payment`, que aunque no sea realmente
importante dada la circumstancia, nos será útil para identifcar nombres de 
servicios pertenecientes a nuestro Bundle.

Nuestro método de pago estará siempre disponible.

## Calling for Shipping methods

Imaginemos que nuestro cart está terminado y queremos agarlo. La plataforma de
Elcodi tan solo ofrece una sola forma de recolectar dichos métodos.

Es importante entender que Elcodi en ningún momento tratará de lanzar ningún 
evento relacionado con el pago de un Order, pues será cada una de las 
plataformas y sus adaptaciones al proyecto los que se encargarán de lanzar sus
propios eventos.

En otras palabras, Elcodi solo propone un entry point al sistema de pago.

En el momento que queramos recolectar todos los métodos de pago, vamos a 
utilizar el servicio creado específicamente para ello.

``` php
$paymentMethods = $this
    ->get('elcodi.wrapper.payment_methods')
    ->get();
```

Internamente, este trozo de código lanza un evento llamado 
*["payment.collect"](events.md#payment.collect)*, encargado exclusivamente de
recolectar todos estos métodos de pago.

Es necesario decir que en este punto, el componente Event Dispatcher de Symfony
no se está utilizando de forma estricta a su definición, pues sé utiliza como 
colector y no como evento. La diferencia es que un evento debería ser inmutable
(un evento pasa y no hay mucho que hacer, solo reaccionar con los event 
listeners), pero en este caso, el objeto evento tiene un método `add`, por lo
que, aún no importando realmente el orden de ejecución de los listeners, se está
modificando el propio evento. Un evento que cambia... esto es raro, verdad?

* [Implement a collector](../cookbook/implementation/implement-a-collector.md)

Pero bueno, el evento de recolección es lanzado, y todos los Plugins que están
escuchados son llamados a añadir sus métodos de pago.

## Adding new Payment Method

Para tí, programador, que quieres añadir un método de envío, este es tu sitio.
Recuerdas el evento *["shipping.collect"](events.md#shipping.collect)*? Pues 
bien, debemos escucharlo para añadir nuestras formas de envío.

Por ahora, ningún objeto se tiene en cuenta para decidir si un método de pago
está disponible o no, por lo que en principio solo se debería tener en cuenta
el estado del propio Plugin.

Veamos primero como podemos suscribirnos a este evento utilizando la misma forma
en que nos suscribimos a cualquier otro evento.

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

Bien. En este Event Listener deberemos poner toda nuestra lógica de negocio.
Veamos un simple ejemplo de como podemos hacer dicha implementación.

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

Como puedes observar, a la hora de crear la nueva instancia de `PaymentMethod`
puedes añaidr distintos elementos como `url`, `imageUrl` y `script`.

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

Esto es porque, en la gran mayoría de casos, una plataforma de pagos, o en su
defecto cualquiera de sus integraciones, proporciona uno de estos dos 
escenarios

* Proporciona una url de salto con una imágen asociada. En este caso, suele ser
habitual cuando trabajamos con pagos donde el usuario salta de dominio para
realizar el pago. En este caso deberíamos utilizar `url` y `imageUrl`.
* Proporciona un script para el pago. Caso habitual para pagos internos sin 
salto de página con llamadas asíncronas mediante Javascript. En este caso
deberíamos utilizar `script` y `imageUrl` en caso que fuera necesario.

## Payment scripts

Para trabajar con plataformas que requieren trozos grandes de javascript, o 
simples trozos de html, es una buena práctica utilizar platillas de Twig.

Veamos un ejemplo un poco más complejo, utilizando scripts específicos y la 
utilización del router para crear rutas.

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

Una vez hemos añadido todos nuestros métodos de pago, deberíamos poder acceder a
toda la información para poder ofrecer las distintas formas de pago al usuario.
Vemos un trozo de código de nuestro template.

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

Como puedes ver se tratan ambos casos (con script o sin él). Cada uno de los
elementos en el array con nombre `paymentMethods` es cada uno de los objetos que
las plataformas de pago han añadido previamente.

Otra forma de acceder a los métodos de pago directamente desde el template es
utilizando la función de twig

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

Bamboo por defecto propone un diagrama de estados de pago como el que podemos
ver aqui.

| From    | Action | To       |
|---------|--------|----------|
| unpaid  | pay    | paid     |
| paid    | refund | refunded |

y mediante el Container de Symfony tenemos acceso a algunos objetos específicos
de la máquina de estados de Pago.

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

Con dicho acceso a la máquina de estados de pago, podemos cambiar el order de
estados utilizando el servicio `elcodi.order_payment_states_machine`.

Veamos un ejemplo de como avanzar a un estado `paid` teniendo en cuenta que
nuestro Order está actualmente en el estado `unpaconfid`.

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

Solo si dicha transición es posible todo funcionará debidamente. En caso 
contrario las excepciones pertinentes saltarán.
