How to Overwrite an Entity
==========================

Each project is completely different, right? Elcodi is just one approach to an
specific e-commerce problem, but one of our goals is to provide a way for
overwriting absolutely everything.

In this document we will show you how to properly overwrite any default entity
implementation with your own.

### Extending or Implementing

Each entity has its own interface. It means that if you want to build your own
`Cart`, for example, your new implementation will need to implement the existing
Cart interface, in that case, 
`Elcodi\Component\Cart\Entity\Interfaces\CartInterface`.

``` php
use Elcodi\Component\Cart\Entity\Interfaces\CartInterface;

/**
 * New Cart implementation
 */
class NewCart implements CartInterface
{
    
}    
```

You can also create a new entity extending the default Cart implementation (at this point
your new entity will be implementing this interface by default). The advantage is
that you don't need to implement all the methods of the interface, but just overwrite
those that you want to change.

``` php
use Elcodi\Component\Cart\Entity\Cart;

/**
 * New Cart implementation
 */
class NewCart extends Cart
{
    
}    
```

Both ways are valid depending on your implementation needs.

### Using this new implementation

Once your new entity is implemented, you need to tell your application
about your new implementation.

Elcodi works with a project called 
[SimpleDoctrineMapping](https://github.com/mmoreram/SimpleDoctrineMapping). This
library enables each bundle to specify how its entities must be treated inside
the Symfony installation, instead of letting Doctrine decide this stuff using 
`auto_mapping=true`.

Each Elcodi Bundle uses this library, so you can overwrite this information 
through each Bundle configuration. Let's see how CartBundle allows the user to
define this information by exposing its configuration.

``` yaml
# app/config/config_local.yml
#
# Per project config file
#
# You can add here your specific configuration. It is recomended
# to add configurations here instead of changing config.yml
#
elcodi_cart:
    
    mapping:
        cart:
            # Cart entity implementing CartInterface
            class: Elcodi\Component\Cart\Entity\Cart
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCartBundle/Resources/config/doctrine/Cart.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
```

If you don't define anything, then these values are injected as default 
configuration values in CartBundle, so this is the way of overwriting the 
definition to start using our new Cart implementation.

``` yaml
# app/config/config_local.yml
#
# Per project config file
#
# You can add here your specific configuration. It is recomended
# to add configurations here instead of changing config.yml
#
elcodi_cart:
    
    mapping:
        cart:
            # Cart entity implementing CartInterface
            class: My\New\Bundle\Entity\NewCart
```

And that's enough.

### Changing the mapping file

Once you have changed your entity, or just using the default entity 
implementation, maybe you want to change the way the entity is mapped in your
database.

``` yaml
# app/config/config_local.yml
#
# Per project config file
#
# You can add here your specific configuration. It is recomended
# to add configurations here instead of changing config.yml
#
elcodi_cart:
    
    mapping:
        cart:
            # Doctrine mapping file for this entity
            mapping_file: '@MyNewBundle/Resources/config/doctrine/Cart.orm.yml'
```

In Elcodi we are actually using `yml` files for such definition, but you can use
both `yml` and `xml` files.

``` yaml
# app/config/config_local.yml
#
# Per project config file
#
# You can add here your specific configuration. It is recomended
# to add configurations here instead of changing config.yml
#
elcodi_cart:
    
    mapping:
        cart:
            # Doctrine mapping file for this entity
            mapping_file: '@MyNewBundle/Resources/config/doctrine/Cart.orm.xml'
```

The result will be the same, and the system will detect the file extension.

### Using another Entity Manager

By default, all entities are managed by the default entity manager. You can 
change this behavior just overwriting this configuration value. The selected entity
manager must exist in your Doctrine ORM definition.

``` yaml
# app/config/config_local.yml
#
# Per project config file
#
# You can add here your specific configuration. It is recomended
# to add configurations here instead of changing config.yml
#
elcodi_cart:
    
    mapping:
        cart:
            # Doctrine manager name
            manager: another_entity_manager
```

Take into account that related entities should share the same entity manager. For
example, in this example, because `Cart` has several `CartLine` instances, it
would be not possible to have both tables managed separately by two different
managers.

### Disabling an Entity

Maybe you want to disable a specific entity. The most used strategy would be not
to use this entity, even if the table is created, but we strongly think that 
your model must be considered an essential part of your implementation, so you 
can disable an entity with a single line.

``` yaml
# app/config/config_local.yml
#
# Per project config file
#
# You can add here your specific configuration. It is recomended
# to add configurations here instead of changing config.yml
#
elcodi_cart:
    
    mapping:
        cart:
            enabled: false
```
