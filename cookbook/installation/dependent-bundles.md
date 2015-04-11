How to Install dependent Bundles
================================

This chapter is not only to show how to install a Dependent Bundle in Elcodi 
environment, but to show how to do it properly.

### Package dependencies

All Bundles in Elcodi follow the same installation strategy than Symfony 
standard packages. Elcodi uses composer for package dependencies, so each
standalone Elcodi repository comes with a properly configured `composer.json` 
file.

But when we talk about Bundles in Symfony, defining the dependencies in the
composer file is not enough. The Kernel will instance only the Bundles you
define in `AppKernel.php` file, ignoring the real dependencies this Bundle has.

So, regarding this real problem, Elcodi has developed some business logic in
order to solve this problem.

First of all, let's take a look at a simple Symfony Bundle class.

``` php
/**
 * ElcodiCartBundle Bundle
 */
class ElcodiCartBundle extends Bundle
{
}
```

Maybe this Bundle needs the instantiation of another bundle (previously added in
the composer definition), but is not defined anywhere. So... let's do that.

The first step is to define a set of Bundle namespaces that must be instanced
before this Bundle.

All dependent Bundles will be instanced before this Bundle. This is because a
Bundle should be able to override another Bundle definition, and Symfony has in
most cases a **Last defined wins** strategy.

``` php
use Symfony\Component\HttpKernel\Bundle\Bundle;
use Elcodi\Bundle\CoreBundle\Interfaces\DependentBundleInterface;

/**
 * ElcodiCartBundle Bundle
 */
class ElcodiCartBundle extends Bundle implements DependentBundleInterface
{
    /**
     * Create instance of current bundle, and return dependent bundle namespaces
     *
     * @return array Bundle instances
     */
    public static function getBundleDependencies()
    {
        return [
            'Elcodi\Bundle\UserBundle\ElcodiUserBundle',
            'Elcodi\Bundle\ProductBundle\ElcodiProductBundle',
            'Elcodi\Bundle\CurrencyBundle\ElcodiCurrencyBundle',
            'Elcodi\Bundle\StateTransitionMachineBundle\ElcodiStateTransitionMachineBundle',
            'Elcodi\Bundle\ShippingBundle\ElcodiShippingBundle',
            'Elcodi\Bundle\ConfigurationBundle\ElcodiConfigurationBundle',
            'Elcodi\Bundle\CoreBundle\ElcodiCoreBundle',
        ];
    }
}
```

So, what else? Is that enough?

Then, in order to make it happen, we must use a specific method in our 
`AppKernel` class, using a Trait provided by `ElcodiCoreBundle`.

``` php
use Elcodi\Bundle\CoreBundle\Traits\BundleDependenciesResolver;
use Elcodi\Bundle\TestCommonBundle\Functional\Abstracts\AbstractElcodiKernel;

/**
 * Class AppKernel
 */
class AppKernel extends AbstractElcodiKernel
{
    use BundleDependenciesResolver;
    
    /**
     * Register application bundles
     *
     * @return array Array of bundles instances
     */
    public function registerBundles()
    {
        return $this->getBundleInstances([
            '\Elcodi\Bundle\CartBundle\ElcodiCartBundle',
        ]);
    }
}
```

And that's it. Your kernel will instance your `ElcodiCartBundle` bundle and all
its dependencies.

> This method will work as well, even if the Bundle has no dependencies or is
> not extending DependentBundleInterface.

### Using dependencies in your project

If you want to use this classes and methods in your Symfony project, you don't 
really need to instance `ElcodiCoreBundle` because you will not need any kind
of Dependency Injection definition.

You only need to add `elcodi/core-bundle` in your package requirements and then
use the Interface and the Trait as is shown in this CookBook entry.