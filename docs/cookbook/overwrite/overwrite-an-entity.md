# How to Overwrite an Entity

Each project is completely different, right? Elcodi is just one approach to an
specific e-commerce problem, but one of our goals is to provide a way for
overwriting absolutely everything.

## Scenario

Your E-commerce installation is great, but the Product implementation that 
Elcodi proposes to you as a base is not enough for your model requirements.

Your Product needs an extra flag called `promoted`, in order to assign an extra
promotional value from 0 to 1, and used when products are required for 
pagination. Your Product, based on Elcodi's one, should have this new field.

## Solution

The solution is very easy. From this moment your Product implementation will be 
the one used in your project, instead of the old one. This means that you will
be able to extend it as much as you want without modifying the old one, just
extending or re-implementing it.

### Extending or Implementing

Each entity has its own interface. It means that if you want to build your own
`Product`, for example, your new implementation will need to implement the 
existing ProductInterface, in that case, 
`Elcodi\Component\Product\Entity\Interfaces\ProductInterface`.

``` php
use Elcodi\Component\Product\Entity\Interfaces\ProductInterface;

/**
 * New Product implementation
 */
class NewProduct implements ProductInterface
{
    ...
}    
```

This is useful when you really want to implement another Product completely 
different than the old one, but with same specification.

You can also create a new entity extending the default Cart implementation (at 
this point your new entity will be implementing this interface by default). The 
advantage is that you don't need to implement all the methods of the interface, 
but just overwrite those that you want to change.

Perfect if you only need to extend it.

``` php
use Elcodi\Component\Product\Entity\Product;

/**
 * New Product implementation
 */
class NewProduct extends Product
{
    /**
     * @var boolean
     *
     * Promoted value
     */
    protected $promoted;
     
     /**
      * Get promoted value
      *
      * @return float Promoted value
      */
     public function getPromoted()
     {
        return $this->promoted;
     }
     
     /**
      * Set promoted value
      *
      * @param float $promoted Promoted value
      *
      * @return $this Self object
      */
     public function setPromoted($promoted)
     {
        $this->promoted = $promoted;
     
        return $this;
     }
}    
```

Both ways are valid depending on your implementation needs, but given our needs,
we will use this one.

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
elcodi_product:
    
    mapping:
        product:
            # Product entity implementing ProductInterface
            class: Elcodi\Component\Product\Entity\Product
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiProductBundle/Resources/config/doctrine/Product.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
```

If you don't define anything, then these values are injected as default 
configuration values in ProductBundle, so this is the way of overwriting the 
definition to start using our new Product implementation.

``` yaml
elcodi_product:
    
    mapping:
        product:
            # Product entity implementing ProductInterface
            class: My\New\Bundle\Entity\NewProduct
```

And that's enough.

### Changing the mapping file

Once you have changed your entity, or just using the default entity 
implementation, maybe you want to change the way the entity is mapped in your
database.

``` yaml
elcodi_product:
    
    mapping:
        product:
            # Product entity implementing ProductInterface
            class: My\New\Bundle\Entity\NewProduct
            # Doctrine mapping file for this entity
            mapping_file: '@MyNewBundle/Resources/config/doctrine/Product.orm.yml'
```

In Elcodi we are actually using `yml` files for such definition, but you can use
both `yml` and `xml` files.

``` yaml
elcodi_product:
    
    mapping:
        product:
            # Product entity implementing ProductInterface
            class: My\New\Bundle\Entity\NewProduct
            # Doctrine mapping file for this entity
            mapping_file: '@MyNewBundle/Resources/config/doctrine/Product.orm.xml'
```

The result will be the same, and the system will detect the file extension.

### Overwriting the mapping file

Once you need to overwrite the mapping file, you need to do it completely. 
Doctrine implementation does not allow overwriting a mapping file, so you need
to copy/paste the old one and modify as you wish.

In fact, this is the best option. As long as you don't use anymore the mapping 
file proposed by Elcodi, then you should take the control of all the mapping 
file.

### Using another Entity Manager

By default, all entities are managed by the default entity manager. You can 
change this behavior just overwriting this configuration value. The selected 
entity manager must exist in your Doctrine ORM definition.

``` yaml
elcodi_product:
    
    mapping:
        product:
            # Product entity implementing ProductInterface
            class: My\New\Bundle\Entity\NewProduct
            # Doctrine mapping file for this entity
            mapping_file: '@MyNewBundle/Resources/config/doctrine/Product.orm.yml'
            # Doctrine manager name
            manager: another_entity_manager
```

Take into account that related entities should share the same entity manager. 
For example, in this example, because `Product` is related to `Category`, it 
would be not possible to have both tables managed separately by two different 
managers.

### Disabling an Entity

Maybe you want to disable a specific entity. The most used strategy would be not
to use this entity, even if the table is created, but we strongly think that 
your model must be considered an essential part of your implementation, so you 
can disable an entity with a single line.

``` yaml
elcodi_product:
    
    mapping:
        product:
            enabled: false
```