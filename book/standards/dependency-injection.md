Dependency Injection
====================

We care as well on how Elcodi services are defined in the Symfony 
Dependency Injection container, in order to make easier for the final user to
remember the name of the service given the namespace of the class.

First of all what you need to know is that all services in Elcodi ecosystem 
start with `elcodi.`.

For example

``` yml
elcodi.event_dispatcher.cart
elcodi.manager.cart
```

We have structured all definitions in three or four levels.

### Three levels services

Most of services will have three levels of definition. Let's analyze previous
examples:

``` yml
elcodi.event_dispatcher.cart
```

**First level** is always `elcodi`. This will help you identifying all Elcodi's
definition spectre.

**Second level** is the group or the final meaning of such service. For example,
in this example our service will be an EventDispatcher.
We have some examples of different kind of services in our project

``` yml
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

### Four levels services

We use four levels of definition when we define adapters.

**First level** is always `elcodi`. This will help you identifying all Elcodi's
definition spectre.

**Second level** is always `adapter`. This will help you identifying that such
service is not a permanent class but an adapter of an existing port.

**Third level** is the name of the port. In this example our port is
`currency_exchange_rate`, a way of calculating rates between currencies.

``` yml
elcodi.adapter.currency_exchange_rate.open_exchange
```

**Fourth level** is the name of the adapter. In the last example, our adapter is
`open_exchange`, a website that implements this feature.