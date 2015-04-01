How to Implement a Factory
==========================

Factories usage is one of the most distinctive things in Elcodi architecture
style. Every single entity in Elcodi ecosystem is created using its own Factory,
so by default, an entity never should define its own construction state.

We strongly believe that an entity is not responsible of how should be created,
so the factory has this responsibility. This means that an entity never should
implement the `__construct()` method and never should initialize any variable.

Because in Elcodi all entity namespaces are defined by a parameter, any factory
must use these parameters to create an instance.

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
designed to be an abstraction of a Factory. Extending this abstract class, you
can access to `$this->getEntityNamespace()`.

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
