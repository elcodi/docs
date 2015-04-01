How to Implement a Form Type
============================

A FormType object in Elcodi ecosystem must follow some rules in order to comply
the project flexibility.

### Using Factories

Remember that every single entity must be created given its factory in Elcodi
Bundles. For more information, please see [Factories](factory.md). In order to
use this feature in Symfony Form Types, we must inject always the related 
entity factory.

``` php
use Elcodi\Component\Core\Factory\Abstracts\AbstractFactory;

/**
 * @var AbstractFactory
 *
 * Factory
 */
protected $factory;

/**
 * Sets Factory
 *
 * @param AbstractFactory $factory Factory
 *
 * @return $this Self object
 */
public function setFactory(AbstractFactory $factory)
{
    $this->factory = $factory;

    return $this;
}
```

After the factory is injected, we can use it in our FormType specification for
defining the `empty_data` value and the `data_class`.

``` php
/**
 * Default form options
 *
 * @param OptionsResolverInterface $resolver
 *
 * @return array With the options
 */
public function setDefaultOptions(OptionsResolverInterface $resolver)
{
    $resolver->setDefaults([
        'empty_data' => function () {
            $this
                ->factory
                ->create();
        },
        'data_class' => $this
            ->factory
            ->getEntityNamespace(),
    ]);
}
```

In order to make things easier, there is a Trait in package `elcodi/core`, 
designed as a Factory container. To use it, just add it in your class, like any
other trait, and you will be able to use the `$this->factory` in your class.

``` php
use Elcodi\Component\Core\Factory\Traits\FactoryTrait;

/**
 * Class BannerType
 */
class BannerType extends AbstractType
{
    use FactoryTrait;
    
    /**
     * Default form options
     *
     * @param OptionsResolverInterface $resolver
     *
     * @return array With the options
     */
    public function setDefaultOptions(OptionsResolverInterface $resolver)
    {
        $resolver->setDefaults([
            'empty_data' => function () {
                $this
                    ->factory
                    ->create();
            },
            'data_class' => $this
                ->factory
                ->getEntityNamespace(),
        ]);
    }
}
```

### Using namespaces

If your FormType has any kind of relation with another entity, you will need to
define this relation using a namespace. In order to comply again with the
flexibility, we need to inject all namespaces using the entity manager 
parameter.

These namespaces in Elcodi must be injected through the class constructor.

``` php
/**
 * Class BannerType
 */
class BannerType extends AbstractType
{
    use FactoryTrait;

    /**
     * @var string
     *
     * Image namespace
     */
    protected $imageNamespace;

    /**
     * Construct
     *
     * @param string $imageNamespace Image namespace
     */
    public function __construct($imageNamespace)
    {
        $this->imageNamespace = $imageNamespace;
    }

    /**
     * Buildform function
     *
     * @param FormBuilderInterface $builder the formBuilder
     * @param array                $options the options for this form
     */
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('image', 'entity', [
                'class'    => $this->imageNamespace,
                'required' => false,
                'label'    => 'image',
                'multiple' => false,
            ]);
    }
}
```
    
### FormType as a service

We are using dependencies in our FormTypes, so remember that we need to define 
our classes as services.

``` yaml
services:

    #
    # Form Types
    #
    elcodi.admin.banner.form_type.banner:
        class: Elcodi\Admin\BannerBundle\Form\Type\BannerType
        arguments:
            - %elcodi.entity.image.class%
        calls:
            - [setFactory, [@elcodi.factory.banner]]
        tags:
            - { name: form.type, alias: elcodi_admin_banner_form_type_banner }
```

The alias of the FormType must be the same than the value returned by the method
`getName()` of the class

``` php
/**
 * Return unique name for this form
 *
 * @return string
 */
public function getName()
{
    return 'elcodi_admin_banner_form_type_banner';
}
```
