How to overwrite a service
==========================

To overwrite a service you must create a new class in your project, extend the 
main service and overwrite the methods you need to overwrite.

``` php
use Elcodi\Component\Cart\Services\CartManager;

/**
 * New cart manager
 */
public function NewCartManager extends CartManager
{
    /**
     * Adds cartLine to Cart
     *
     * This method dispatches all Cart Check and Load events
     * It should NOT be used to add a Purchasable to a Cart,
     * by manually passing a newly crafted CartLine, since
     * no product duplication check is performed: in that
     * case CartManager::addProduct should be used
     *
     * @param CartInterface     $cart     Cart
     * @param CartLineInterface $cartLine Cart line
     *
     * @return $this Self object
     */
    protected function addLine(
        CartInterface $cart,
        CartLineInterface $cartLine
    ) {
    
        // My own addLine implementation
    }
}
```

Finally we need to make the project know that this new implementation is the one
we really want to use in our project. For this reason, we should redefine this
service in our `config.yml` file, and overwrite the main service implementation.

``` yaml
services:

    elcodi.manager.cart:
        cart: My\New\Bundle\Services\NewCartManager
        arguments:
            ...
```

Done that, all the project will use your `elcodi.manager.cart` implementation 
instead of the default one.