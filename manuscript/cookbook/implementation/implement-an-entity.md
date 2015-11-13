# How to Implement an Entity

In Elcodi we use an extremely thin model. It means that our entities are just
parameters, getters and setters.

## Scenario

Our website must start managing warehouses. We must be able to create new
warehouses, edit them and deleting them, as well as create new associations 
between warehouses and locations, in order to locate them.

Our model needs to change, so our application should be enough robust to be 
changed without many pain.

## Solution

In order to change our model, we need to create a new entity following the 
standards of Elcodi.

Creating an entity is as easy as defining what an actor should own (parameters),
forgetting about the behavior of this one. This logic will be placed then in the 
service layer.

Let's see a basic implementation of an entity.

``` php
class Warehouse
{
    /**
     * @var name
     *
     * Product name
     */
    protected $name;
     
    /**
     * Get name
     *
     * @return string Product name
     */
    public function getName()
    {
        return $this->name;
    }
    
    /**
     * Set name
     *
     * @param string $name Product name
     *
     * @return $this Self object
     */
    public function setName($name)
    {
        $this->name = $name;
        
        return $this;
    }
}
```

As you can see all properties are defined as protected. This is because Doctrine
will need to extend the class using [transparent Proxy Objects](http://doctrine-orm.readthedocs.org/en/latest/reference/advanced-configuration.html#proxy-objects)
to implement lazy-loading.

### Using Interfaces

All Elcodi entities are an implementation of a [
Header Interface](http://martinfowler.com/bliki/HeaderInterface.html).
We use some 
[Role Interfaces](http://martinfowler.com/bliki/RoleInterface.html) in
our code, but we are more focused in the first one because is more 
understandable for everyone when overriding default implementations.

``` php
use MyComponent\Warehouse\Entity\Interfaces\WarehouseInterface;

/**
 * Class Warehouse
 */
class Warehouse implements WarehouseInterface
{
    // ...
}
```

### Using SimpleDoctrineMapping

Usually, final projects need to implement more entities inside their projects.
Since now, this has been so easy but so magical, and once again, we really think
that this stuff should never be magic at all. Is your model, your business.

In our actual implementation, we are using an Open Source project called 
[SimpleDoctrineMapping](https://github.com/mmoreram/SimpleDoctrineMapping). 
You should read carefully the documentation, indeed we strongly recommend it.

Yes, we need to say that, we'll need to disable the `auto_mapping` configuration 
item inside DoctrineBundle. This flag allow this magic we really want to avoid.

Instead of that, we will specifically define how our entities should be defined
and treated inside our project.

## Related links

* [SimpleDoctrineMapping Docs](https://github.com/mmoreram/SimpleDoctrineMapping)