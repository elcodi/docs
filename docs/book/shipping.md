# Shipping

Bueno, ahora toca implementar un sistema de Shipping.
Si has llegado a este punto significa que has podido trabajar en algunos de los
puntos más interesantes de Elcodi, especialmente los relacionados con Cart y 
Product.

En este apartado explicaremos todo lo relacionado con el Shipping en Elcodi. 
Como añadir nuevos métodos de envío de forma desacoplada o como cambiar los 
estados de envío de un Order ya creado.

## Requisites

Para entender este apartado es importante tener una serie de puntos bien
asimilados, todos ellos referentes a algunos componentes de Symfony, así como
ciertas prácticas de arquitectura.

A continuación te ofrecemos una serie de enlaces donde puedes informarte un poco
más.

* [Plugins](plugins.md)
* [Dependency Injection Symfony Component](http://symfony.com/doc/current/components/dependency_injection/introduction.html)
* [Event Dispatcher Symfony Component](http://symfony.com/doc/current/components/event_dispatcher/introduction.html)
* [Elcodi State Transition Machine Component](../component/state-transition-machine.md)
* [Finite State Machine](https://en.wikipedia.org/wiki/Finite-state_machine)

## Example

Imaginemos que queremos añadir un sistema de shipping, cuyo único método se
llama *envío gratis* y es añadido solo cuando el valor del Cart es superior a
los 30 euros y el usuario pertenece o a Barcelona o a Madrid. Esta lógica de 
negocio puede ser tan compleja como sea necesario, por lo que la vamos a 
esconder tras un método llamado `isCartSatisfied()`.

Lo más normal es que se utilizen librerías externas de PHP para dichas 
integraciones, por lo que toda la documentación al respecto será relativa a como
implementar utilizar dichas integraciones.

## Calling for Shipping methods

Uno de los componentes de Elcodi es el de Shipping. Dicho componente tiene un
solo objetivo, y es proporcionar al proyecto una forma limpia y desacoplada de
añadir opciones de envío al comprador.

Para esto debemos tener acceso al Cart del comprador, pues este será el objeto
con el que podremos determinar cuales de los métodos de envío son accesibles.

En el momento en que nuestro sistema acepta que no habrá más cambios de Cart (no
da opción a añadir más productos o coupones), y para recopilar todos los métodos
de envío accesibles, vamos a tener este trozo de código.

``` php
$cart = $this
    ->container
    ->get("elcodi.wrapper.cart")
    ->get();
    
$shippingMethods = $this
    ->get('elcodi.wrapper.shipping_methods')
    ->get($cart);
```

Internamente, este trozo de código lanza un evento llamado 
*["shipping.collect"](events.md#shipping.collect)*, encargado exclusivamente de
recolectar todos estos métodos de envío.

Es necesario decir que en este punto, el componente Event Dispatcher de Symfony
no se está utilizando de forma estricta a su definición, pues sé utiliza como 
colector y no como evento. La diferencia es que un evento debería ser inmutable
(un evento pasa y no hay mucho que hacer, solo reaccionar con los event 
listeners), pero en este caso, el objeto evento tiene un método `add`, por lo
que, aún no importando realmente el orden de ejecución de los listeners, se está
modificando el propio evento. Un evento que cambia... esto es raro, verdad?

* [Implement a collector](../cookbook/implementation/implement-a-collector.md)

Pero bueno, el evento de recolección es lanzado, y todos los Plugins que están
escuchados son llamados a añadir sus métodos a partir del Cart proporcionado
por el evento

## Adding new Shipping Methods

Para tí, programador, que quieres añadir un método de envío, este es tu sitio.
Recuerdas el evento *["shipping.collect"](events.md#shipping.collect)*? Pues 
bien, debemos escucharlo para añadir nuestras formas de envío.

Para nuestro ejemplo, debemos considerar si nuestro Cart cumple las espectativas
que nosotros queremos, y de ser así, añadir un nuevo sistema de envío gratuito.

Veamos primero como podemos suscribirnos a este evento utilizando la misma forma
en que nos suscribimos a cualquier otro evento.

``` yml
services:

    #
    # Event Listeners
    #
    elcodi_plugin.easy_free_shipping.event_listener.shipping_collect:
        class: Elcodi\Plugin\EasyFreeShipping\EventListener\ShippingCollectEventListener
        tags:
            - { name: kernel.event_listener, event: shipping.collect, method: addCustomShippingMethods }
```

Bien. En este Event Listener deberemos poner toda nuestra lógica de negocio.
Veamos un simple ejemplo de como podemos hacer dicha implementación.

``` php

use Elcodi\Component\Cart\Entity\Interfaces\CartInterface;
use Elcodi\Component\Currency\Entity\Money;
use Elcodi\Component\Shipping\Entity\ShippingMethod;
use Elcodi\Component\Shipping\Event\ShippingCollectionEvent;

/**
 * Class ShippingCollectEventListener
 */
class ShippingCollectEventListener
{
    /**
     * Look for available shipping methods, by checking given event's cart
     *
     * @param ShippingCollectionEvent $event Event
     *
     * @return $this Self object
     */
    public function addCustomShippingMethods(ShippingCollectionEvent $event)
    {
        $defaultCurrency = // Default currency object
        $cart = $event->getCart();
        if (!$this->isCartSatisfied($cart)) {
        
            return;
        }

        $event
            ->addShippingMethod(new ShippingMethod(
                'easy-free-shipping',
                'Easy free shipping',
                'Exclusive Free shipping',
                'Send your order for free because yes!',
                Money::create(0, $currency)
            ));
    }

    /**
     * Given Cart satisfies this Bundle needs
     *
     * @param CartInterface $cart Cart
     *
     * @return boolean Cart satisfies needs
     */
    private function isCartSatisfied(CartInterface $cart)
    {
        /**
         * Place all your logic here
         */

        return $cartSatisfied;
    }
}
```

Dentro del método `isCartSatisfied()` deberíamos poner nuestra lógica, pero 
recordemos que no debemos poner nuestro foco en dicha implementación.

> This example is an abstract implementation. If such implementation comes from
> a plugin, after adding all methods you should check the plugin availability.
> Check the plugin documentation.

## Accessing collected methods

Una vez hemos recolectado todos nuestros métodos de envío, podemos acceder a 
ellos en nuestra página para que el cliente pueda seleccionar cual de ellos es
el que quiere utilizar. Veamos un ejemplo, partiendo con la premisa de que en 
nuestro controlador hemos devuelto todos los métodos de envío en un array con
nombre `shippingMethods`.

``` jinja
{% if shippingMethods|length > 0 %}
<div class="form form-checkout">
    {% set actualShippingMethod = cart.shippingMethod %}
    <div class="grid grid-pad">
        <h2>Shipping methods</h2>
        {% for shippingMethod in shippingMethods %}
            {% if shippingMethod.id == actualShippingMethod %}
                <div>{{ shippingMethod.carrierName }} - {{ shippingMethod.price|print_convert_money }}</div>
            {% else %}
                <a href="{{ url("store_checkout_shipping_method_apply", {
                        'shippingMethod': shippingMethod.id
                    }) }}">
                        {{ shippingMethod.carrierName }} - {{ shippingMethod.price|print_convert_money }}
                    </a></div>
            {% endif %}
        {% endfor %}
    </div>
</div>
{% endif %}
```

Como podéis ver, en todo momento se tiene en cuenta el valor que hay en 
`cart.shippingMethod`. En él, siempre tenemos referencia al id del método de
envío que se haya seleccionado. En caso de que aún no se haya seleccionado 
ningún método de envío, cosa que pasará siempre al principio del proceso, todos
los tipos estarán seleccionables. En caso contrario, se podrá cambiar de método
seleccionando cualquier otro.

Otra forma de acceder a los métodos de envío directamente desde el template es
utilizando la función de twig

``` jinja
{% set shippingMethods = elcodi_shipping_methods() %}
```

Para seleccionar un método de envío, podemos ver que tenemos disponible una url
cuyo único parámetro, requerido, es el id del método de pago que queremos 
seleccionar.

## Selecting a shipping method

Veamos la implementación que utiliza Elcodi para seleccionar un método de envío
específico.

``` php
$shippingMethodId = $request
    ->query
    ->get('shippingMethod');

$cart = $this
    ->container
    ->get("elcodi.wrapper.cart")
    ->get();
    
$shippingMethods = $this
    ->get('elcodi.wrapper.shipping_methods')
    ->getOneById($cart, $shippingMethodId);
    
if ($shippingMethodObject instanceof ShippingMethod) {
    $cart->setShippingMethod($shippingMethod);
    $this
        ->get('elcodi.object_manager.cart')
        ->flush($cart);
}

return $this->redirect(
    $this->generateUrl('store_checkout_payment')
);
```

Sencillo, conciso y rápido. Como podemos ver, volvemos a utilizar el mismo 
método para recolectar todos los métodos de envío, pues en este caso, en lugar
de devolverlos todos, solo devolvemos aquel con el id deseado.

Si lo recibimos, quiere decir que existe y es válido para el cart utilizado. En
este caso, sobreescribiremos la propiedad `shippingMethod` de cart y guardaremos
el valor en base de datos. En caso contrario, básicamente, no hacemos nada.

En ambos casos, volvemos a la página de pago, ejecutando otra vez el código de
vista anterior, aunque, posiblemente, con un resultado un poco distinto

## Reloading Cart prices

Dado que Elcodi tiene una forma de funcionar bastante desacoplada, no debemos 
hacer nada más para recalcular los precios. En el caso de que seleccione un
método de envío que añada un precio extra al precio final del Cart, éste se 
calculará solo durante los eventos de load del mismo, pues este proceso es 
completamente transparente para cualquier plugin que quiera añadir métodos de
envío.

## Order shipping information

Una vez nuestro Order se ha transformado correctamente en un Order, deberíamos
ser capaces de saber qué tipo de envío se utilizó y que precio tuvo en su 
momento.

``` php
$orderShippingMethod = $order->getShippingMethod();
$orderShippingAmount = $order->getShippingAmount();
```

## Shipping State Machine

At this point, the order is ready to be shipped. Once starting these last steps
on this documentation, please take a look at the State Transition Machine 
documentation.

* [State Transition Machine](../component/state-transition-machine.md)

Bamboo por defecto propone un diagrama de estados de envío como el que podemos
ver aqui.

| From        | Action               | To          |
|-------------|----------------------|-------------|
| preparing   | order ready          | processed   |
| processed   | picked up by carrier | in delivery |
| processed   | picked up on store   | delivered   |
| in delivery | delivered            | delivered   |
| preparing   | cancel               | cancelled   |
| processed   | cancel               | cancelled   |
| in delivery | cancel               | cancelled   |
| in delivery | return               | returned    |
| delivered   | return               | returned    |

y mediante el Container de Symfony tenemos acceso a algunos objetos específicos
de la máquina de estados de Shipping.

``` yaml
#
# Order state machine for Shipping
#
elcodi.order_shipping_states_machine_builder:
    class: Elcodi\Component\StateTransitionMachine\Machine\MachineBuilder
    arguments:
        - @elcodi.factory.state_transition_machine
        - %elcodi.order_shipping_states_machine_identifier%
        - %elcodi.order_shipping_states_machine_states%
        - %elcodi.order_shipping_states_machine_point_of_entry%

elcodi.order.shipping_states_machine:
    class: Elcodi\Component\StateTransitionMachine\Machine\Machine
    factory:
        - @elcodi.order_shipping_states_machine_builder
        - compile

elcodi.order_shipping_states_machine_manager:
    class: Elcodi\Component\StateTransitionMachine\Machine\MachineManager
    arguments:
        - @elcodi.order.shipping_states_machine
        - @event_dispatcher
        - @elcodi.factory.state_transition_machine_state_line
```

## Updating the Shipping state

Con dicho acceso a la máquina de estados de envío, podemos cambiar el order de
estados utilizando el servicio `elcodi.order_shipping_states_machine`.

Veamos un ejemplo de como avanzar a un estado `delivered` teniendo en cuenta que
nuestro Order está actualmente en el estado `processed`.

``` php
$order = // Order instance;

$stateLineStack = $this
    ->get('elcodi.order_shipping_states_machine_manager')
    ->transition(
        $order,
        $order->getShippingStateLineStack(),
        'picked up on store',
        'Order picked up on store'
    );
$order->setShippingStateLineStack($stateLineStack);
$this
    ->get('elcodi.object_manager.order')
    ->flush($order);
```

Solo si dicha transición es posible todo funcionará debidamente. En caso 
contrario las excepciones pertinentes saltarán.
