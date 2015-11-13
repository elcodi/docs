# How to Implement a Factory

Factories usage is one of the most distinctive things in Elcodi architecture
style. Every single entity in Elcodi ecosystem is created using its own Factory,
so by default, an entity should never define its own construction state.

## Scenario

Our project have grown and we have some extra needs. We have created new 
entities, and because we strongly believe that an entity is not responsible of 
how it should be created, the factory should holds this responsibility. 
This means that an entity should never implement the `__construct()` method and 
it should never initialize any variable.

In one of our services we need to create a new entity instance. How easy is 
creating this entity by using `new()`, right? But then, this piece of code is
not testable anymore, so we should be able to mock this new entity.

Let's create a new factory for that new entity!

## Solution

A factory is a simple class that, returns a new entity instance once it knows
how to build it. Because in Elcodi all entity namespaces are defined by a 
parameter, any factory must use these parameters to create an instance.

``` php
use Elcodi\Component\Language\Entity\Interfaces\LanguageInterface;

/**
 * Class LanguageFactory
 */
class LanguageFactory extends AbstractFactory
{
    /**
     * @var string
     *
     * Entity namespace
     */
    protected $entityNamespace;

    /**
     * Set Entity Namespace
     *
     * @param string $entityNamespace Entity namespace
     *
     * @return $this Self object
     */
    public function setEntityNamespace($entityNamespace)
    {
        $this->entityNamespace = $entityNamespace;

        return $this;
    }

    /**
     * Creates an instance of a simple language.
     *
     * This method must return always an empty instance for related entity
     *
     * @return LanguageInterface Empty entity
     */
    public function create()
    {
        /**
         * @var LanguageInterface $language
         */
        $classNamespace = $this->getEntityNamespace();
        $language = new $classNamespace();

        $language->setEnabled(false);

        return $language;
    }
}
```

As you can see, the namespace is injected, and when the factory creates the 
instance, uses this namespace. With this strategy, the factory becomes uncoupled
from the entity implementation.

In order to make things easier, there is a Trait in package `elcodi/core`, 
designed to be an abstraction of a Factory. Extending abstract class 
`AbstractFactory` as is shown in examples, you can easily access to 
`$this->getEntityNamespace()` having the same result as before.

``` php
use Elcodi\Component\Core\Factory\Abstracts\AbstractFactory;
use Elcodi\Component\Language\Entity\Interfaces\LanguageInterface;

/**
 * Class LanguageFactory
 */
class LanguageFactory extends AbstractFactory
{
    /**
     * Creates an instance of a simple language.
     *
     * This method must return always an empty instance for related entity
     *
     * @return LanguageInterface Empty entity
     */
    public function create()
    {
        /**
         * @var LanguageInterface $language
         */
        $classNamespace = $this->getEntityNamespace();
        $language = new $classNamespace();

        $language->setEnabled(false);

        return $language;
    }
}
```

### Factory as a service

Because the factory dependencies need to be resolved, we must define the factory
as a service.

``` yaml
services:

    #
    # Factory for entity language
    #
    elcodi.factory.language:
        class: %elcodi.factory.language.class%
        calls:
            - [setEntityNamespace, ["%elcodi.entity.language.class%"]]
```

Once this factory is created as a service, you should inject this factory in 
your service in order to using them when new entity instance is required.
