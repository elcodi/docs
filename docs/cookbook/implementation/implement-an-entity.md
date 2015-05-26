How to Implement an Entity
==========================

In Elcodi we use an extremely thin model. It means that our entities are just
parameters, getters and setters.

Creating an entity is as easy as defining what an actor should own (parameters),
forgetting about the behavior of this one. This logic will be placed then in the 
service layer.

Let's see a basic implementation of an entity.

``` php
class Product
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

### Interfaces

All Elcodi entities are an implementation of a [
Header Interface](http://martinfowler.com/bliki/HeaderInterface.html).
We use some 
[Role Interfaces](http://martinfowler.com/bliki/RoleInterface.html) in
our code, but we are more focused in the first one because is more 
understandable for everyone when overriding default implementations.

``` php
use Elcodi\Component\Product\Entity\Interfaces\ProductInterface;

/**
 * Class ProductInterface
 */
class Product implements ProductInterface
{
    // ...
}
```

### New entities

Usually, final projects need to implement more entities inside their projects.
Since now, this has been so easy but so magical, and once again, we really think
that this stuff should never be magic at all. Is your model, your business.

In our actual implementation, we are using an Open Source project called 
[Simple Doctrine Mapping](https://github.com/mmoreram/SimpleDoctrineMapping). 
You should read carefully the documentation, indeed we strongly recommend it.

Yes, we need to say that, we'll need to disable the `auto_mapping` configuration 
item inside DoctrineBundle. This flag allow this magic we really want to avoid.

Instead of that, we will specifically define how our entities should be defined
and treated inside our project.
