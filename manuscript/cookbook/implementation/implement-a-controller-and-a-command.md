# How to Implement a Controller or a Command

Controllers and Commands in Elcodi are exactly the same than Controllers and 
Commands in Symfony. No difference between them. But in Elcodi we have a strong
philosophy about how to create them in order to be valid or usable in the Open
Source environment.

## Scenario

We have a Bamboo-based application in our server and we need to create a new 
entry-point for our application, for example a new Controller to manage orders
from specific users, in order to create special newsletters and increase the
number of orders per customer.

Controllers and Commands are designed and implemented following a simple but
specific way. We need to know how both classes are created in Elcodi in order to
follow the same philosophy in all places.

## Solution

Following this specific way, we need to know the differences between creating
Controllers and Commands as services, or simply rely to the framework the way
these classes are auto-discovered.

### Services in Elcodi

All Elcodi Controllers and Commands are defined as services. This is because we
are *Overwriting Oriented Programming* and we have in mind that every single
implementation sensible to be changed between projects must be easily 
customized.

In Symfony, and because Dependency Injection definition has a very well documented
overwriting strategy, we have decided that everything must be defined as a 
service.

We have a convention about how a Controller and a Command must be registered in
your DI container

``` yaml
elcodi.controller.image_upload
elcodi.command.populate_currency_rates
```

Of course, and because it should only depend on the Symfony Components, but never
the Symfony Framework nor the FrameworkBundle, both implementations must not extend
Framework classes.

### Framework Controllers in Bamboo

When you work in Bamboo (or in your project on top of Elcodi Components and 
Bundles) we don't recommend the usage of Controllers and Commands as services.
We think that both implementations are Entry Points and should be Framework
related.

This means that your Controllers or Commands should never have business logic,
and should place all this logic in your service layer.

Having said that, we recommend the usage of *FrameworkBundle* Controller class to
endow your controllers with easy access to the DI container and a good set of
pre-built methods.

``` php
use Symfony\Bundle\FrameworkBundle\Controller\Controller;

/**
 * Cart controllers
 */
class CartController extends Controller
{
}
```

### Framework Commands in Bamboo

We have the same opinion for Commands and Controllers.

``` php
use Symfony\Bundle\FrameworkBundle\Command\ContainerAwareCommand;

/**
 * Cart controllers
 */
class MyCommand extends ContainerAwareCommand
{
}
```

### Annotations in Bamboo Controllers

In Bamboo, we use annotations in all our controllers in order to make the code more
understandable and to focus only on business logic. Annotations solve
repetitive things and should be perfectly documented.

> Magic? Yes, of course, same magic than yml readers, than Container
> compilation, than service builds... so what?

For this reason, we are using 
[ControllerExtraBundle](http://github.com/mmoreram/ControllerExtraBundle) and
[SensioFrameworkExtraBundle](https://github.com/sensiolabs/SensioFrameworkExtraBundle).

Let's see an example.

``` php
use Mmoreram\ControllerExtraBundle\Annotation\Entity as AnnotationEntity;
use Mmoreram\ControllerExtraBundle\Annotation\Form as AnnotationForm;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Symfony\Bundle\FrameworkBundle\Controller\Controller;

/**
 * Cart controllers
 *
 * @Route(
 *      path = "/cart",
 * )
 */
class CartController extends Controller
{
    /**
     * Cart view
     *
     * @param FormView      $formView Form view
     * @param CartInterface $cart     Cart
     *
     * @return Response Response
     *
     * @Route(
     *      path = "",
     *      name = "store_cart_view",
     *      methods = {"GET"}
     * )
     *
     * @AnnotationEntity(
     *      class = {
     *          "factory" = "elcodi.wrapper.cart",
     *          "method" = "loadCart",
     *          "static" = false,
     *      },
     *      name = "cart"
     * )
     * @AnnotationForm(
     *      class = "store_cart_form_type_cart",
     *      name  = "formView",
     *      entity = "cart",
     * )
     */
    public function viewAction(
        FormView $formView,
        CartInterface $cart
    ) {
        // ...
    
        return $this->renderTemplate(
            'Pages:cart-view.html.twig',
            [
                // ...
            ]
        );
    }
}
```

You can find some documentation of both annotation packages

* [ControllerExtraBundle](https://github.com/mmoreram/ControllerExtraBundle/blob/master/README.md)
* [SensioFrameworkExtraBundle](http://symfony.com/doc/current/bundles/SensioFrameworkExtraBundle/index.html)

Please, before using annotations in your project make sure that you know how
they work and how to use them. Read the documentation of both packages and try
to understand their meaning.

## Related links

* [Symfony docs - How to define controllers as services](http://symfony.com/doc/current/cookbook/controller/service.html)
* [Symfony docs - How to define commands as services](http://symfony.com/doc/current/cookbook/console/commands_as_services.html)