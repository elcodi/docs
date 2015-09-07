# Entity Translator

Translate your entities in a very decoupled way.

* [Component Repository](https://github.com/elcodi/EntityTranslator)
* [Bundle Repository](https://github.com/elcodi/EntityTranslatorBundle)

The main goal of this repository is to create an specific translation layer on 
top of any entity, using a simple and soft layer over the mapping doctrine, and
not mixing them, as this would be a bad practice.

## What offers

This package offers, as a bundle, the mechanism of adding automatically all this
layer by using several Doctrine and Form event listeners. With a simple 
configuration can handle all the translations.

## Usage

The model is the most important part of a project, right? We strongly believe
that, so we think that coupling the entities with the translation infrastructure
is a bad practice.

This component provides an external translation engine with some nice services
to make an entity translation something really easy and enjoyable.

Let's figure out we are working with a Product object. Our model clean, with two
fields: name and description. All this documentation assumes the usage of this
class.

``` php
/**
 * Class Product
 */
class Product
{
    /**
     * @var string
     *
     * id
     */
    protected $id;

    /**
     * @var string
     *
     * Name
     */
    protected $name;

    /**
     * @var string
     *
     * Description
     */
    protected $description;

    /**
     * Get Id
     *
     * @return string Id
     */
    public function getId()
    {
        return $this->id;
    }

    /**
     * Sets Id
     *
     * @param string $id Id
     *
     * @return $this Self object
     */
    public function setId($id)
    {
        $this->id = $id;

        return $this;
    }

    /**
     * Get Name
     *
     * @return mixed Name
     */
    public function getName()
    {
        return $this->name;
    }

    /**
     * Sets Name
     *
     * @param mixed $name Name
     *
     * @return $this Self object
     */
    public function setName($name)
    {
        $this->name = $name;

        return $this;
    }

    /**
     * Get Description
     *
     * @return string Description
     */
    public function getDescription()
    {
        return $this->description;
    }

    /**
     * Sets Description
     *
     * @param string $description Description
     *
     * @return $this Self object
     */
    public function setDescription($description)
    {
        $this->description = $description;

        return $this;
    }
}
```

We want to manage the translations of both properties. Important, each project
needs to define what fields must be translatable, this is not related to the
component itself.

## EntityTranslationProvider

The translation provider is responsible to connect with the persistent layer and
provide the results, using an specific format, to the managers and services.

``` php
/**
 * Interface EntityTranslationProviderInterface
 */
interface EntityTranslationProviderInterface
{
    /**
     * Get translation
     *
     * @param string $entityType  Type of entity
     * @param string $entityId    Id of entity
     * @param string $entityField Field of entity
     * @param string $locale      Locale
     *
     * @return string Value fetched
     */
    public function getTranslation(
        $entityType,
        $entityId,
        $entityField,
        $locale
    );

    /**
     * Set translation
     *
     * @param string $entityType       Type of entity
     * @param string $entityId         Id of entity
     * @param string $entityField      Field of entity
     * @param string $translationValue Translated value
     * @param string $locale           Locale
     *
     * @return string Value fetched
     */
    public function setTranslation(
        $entityType,
        $entityId,
        $entityField,
        $translationValue,
        $locale
    );

    /**
     * Flush all previously set translations.
     *
     * @return $this self Object
     */
    public function flushTranslations();
}
```

There is the possibility of using specific providers for some specific
decorations, for example, the `CachedEntityTranslationProvider` implementation,
that decorates the main provider by adding an extra cache layer.

## EntityTranslatorBuilder

Translation configuration is not set on the fly. It means that a Translator
needs to know what entities and what fields must take in account for
translations.

For that reason we need to build the translator using a TranslatorBuilder.

``` php
$entityTranslationProvider;
$entityTranslatorFactory = new TranslatorFactory();
$entityTranslator = $this->getMock(
    'Elcodi\Component\EntityTranslator\Services\EntityTranslator', 
    [], 
    [], 
    '', 
    false
);

$configuration = [
    'Elcodi\Component\EntityTranslator\Tests\Fixtures\TranslatableProduct' => [
        'alias'  => 'product',
        'idGetter' => 'getId',
        'fields' => [
            'name' => [
                'setter' => 'setName',
                'getter' => 'getName',
            ]
        ]
    ],
];

$entityTranslatorBuilder = new EntityTranslatorBuilder(
    $entityTranslationProvider,
    $translatorFactory,
    $configuration
);
$entityTranslator = $translatorBuilder->compile();
```

A compiled translator is, somehow, an immutable object. This means that,
internally, can change because of new instances, but externally can only be
accessed by getters.

## EntityTranslator

The translator itself. This implementation enables you to work with entities
instead of translations.

``` php
/**
 * Interface EntityTranslatorInterface
 */
interface EntityTranslatorInterface
{
    /**
     * Translate object
     *
     * @param Object $object Object
     * @param string $locale Locale to be translated
     */
    public function translate($object, $locale);

    /**
     * Saves object translations
     *
     * @param Object $object       Object
     * @param array  $translations Translations
     */
    public function save($object, array $translations);
}
```

Using the same configuration than before, lets see an example about how we can
manage our entity translations using this service.

``` php
$product = new TranslatableProduct();
$product
    ->setId(1)
    ->setName('productName');

$entityTranslator->save($product, [
    'es' => [
        'name' => 'el nombre',
        'description' => 'la descripción',
    ],
    'en' => [
        'name' => 'the name',
        'description' => 'the description',
        'anotherfield' => 'some value',
    ],
]);

/**
 * At this point, all the data have been persisted to database and cached.
 * As you can see, the `translate` method returns the translated object, but
 * both objects are the same
 */

$translatedProduct = $translator->translate($product, 'en');

$translatedName = $translatedProduct->getName();
echo $translatedName;
// = "the name"

/**
 * So because both object are the same, you can just apply the translation
 * without using the returned object
 */

$translator->translate($product, 'es');

$translatedName = $product->getName();
echo $translatedName;
// = "el nombre"

```

To when you save all the translations of an entity, the `EntityTranslator` will
make a unique flush to the persistence layer, in order to improve this action.

## Using Doctrine

Easy and simple. Let's work with a simple entity mapped in Doctrine.

``` php
/**
 * Class Product
 *
 * @ORM\Entity
 */
class Product
{
    /**
     * @var string
     *
     * @ORM\Id
     * @ORM\Column
     * @ORM\GeneratedValue
     *
     * id
     */
    protected $id;

    /**
     * @var string
     *
     * @ORM\Column(type="string")
     *
     * Name
     */
    protected $name;

    /**
     * @var string
     *
     * @ORM\Column(type="text")
     *
     * Description
     */
    protected $description;
}
```

This example will assume that you want to make both fields, name and
description, translatable. To make it happen, we can simply remove  the mapping
of both fields, to rely the responsibility of translation to the Translator
component.

``` php
/**
 * Class Product
 *
 * @ORM\Entity
 */
class Product
{
    /**
     * @var string
     *
     * @ORM\Id
     * @ORM\Column
     * @ORM\GeneratedValue
     *
     * id
     */
    protected $id;

    /**
     * @var string
     *
     * Name
     */
    protected $name;

    /**
     * @var string
     *
     * Description
     */
    protected $description;
}
```

Now your installation will see the Product as a simple id. Why is this good for
us? This example uses Annotations, and we highly recommend to define all your
mapping configuration using `yml` so you can easily decouple your model
implementation (entities) and your mapping configuration (Product.orm.yml) and
disable some fields for mapping with the same entity implementation.

## Configuring the entity translator

Now, we need to configure the translator. You need to define what entities and
fields you allow the engine to manage. Let's see the configuration for this
example.

``` yml
elcodi_entity_translator:
    configuration:
        My\Bundle\Entity\Product:
            alias: product

            // idGetter, by default, getId
            idGetter: getId
            fields:
                name:
                    // getter, by default, getName in this case
                    // setter, by default, setName in this case
                    getter: getName
                    setter: setName
                description:
                    getter: getDescription
                    setter: setDescription
```

This is the basic definition of the Product translation. You can see that there
is come specific information set by default, so indeed, this configuration could
be defined as well that way

``` yml
elcodi_entity_translator:
    configuration:
        My\Bundle\Entity\Product:
            alias: product
            fields:
                name: ~
                description: ~
```

You can use Interfaces as well, so in that case, to ensure that the
configuration will be still valid even if you change the Product implementation,
you can use an Interface for that

``` yml
elcodi_entity_translator:
    configuration:
        My\Bundle\Entity\Interfaces\ProductInterface:
            alias: product
            fields:
                name: ~
                description: ~
```

## Saving translations

Do you want to translate an entity? Great, let's do that! You can easily manage
your translation using the entity translator service.

``` php
$entityTranslator = $this
    ->container
    ->get('elcodi.entity_translator');
```

Once you have the service instance, you can set your entity translations in a
very simple way. In this example, let's create a new entity, let's save it into
database and let's translate it.

``` php
$product = new Product();
$product->setId(1);

$entityTranslator->save($product, [
    'es' => [
        'name' => 'el nombre',
        'description' => 'la descripción',
    ],
    'en' => [
        'name' => 'the name',
        'description' => 'the description',
        'anotherfield' => 'some value',
    ],
]);
```

## Translating an entity

So, your entity is translated? Well, let's load the translations of an entity.
You only need the entity and the language you want it to be translated to.

``` php
$product = $this
    ->get('doctrine.orm.entity_manager')
    ->find(1);

$entityTranslator->translate($product, 'en');
```

And that's it. Your product is now translated to english, and because both
fields, name and description, are not mapped anymore, you could flush the entity
manager and no information would be saved.

## Translations in forms

This is maybe the most important feature of this Bundle. How can we handle all
this stuff in our forms? Very simple. We don't have to change our form
definition but only adapt it to add an EventSubscriber.

Let's see an example.

``` php
use Elcodi\Component\EntityTranslator\EventListener\Traits\EntityTranslatableFormTrait;
use Symfony\Component\Form\AbstractType;

/**
 * Class ProductType
 */
class ProductType extends AbstractType
{
    use EntityTranslatableFormTrait;

    /**
     * Buildform function
     *
     * @param FormBuilderInterface $builder the formBuilder
     * @param array                $options the options for this form
     */
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('name', 'text', [
                'required' => true,
                'label'    => 'name',
            ])
            ->add('description', 'textarea', [
                'required' => false,
                'label'    => 'description',
            ]);

        $builder->addEventSubscriber($this->getEntityTranslatorFormEventListener());
    }

    /**
     * Return unique name for this form
     *
     * @return string
     */
    public function getName()
    {
        return 'form_type_product';
    }
}
```

As you can see, there are only two changes in this formType. First one is that
you must add this trait in order to add a protected variable and two methods.
The second change is that you must add the EventSubscriber the way is shown in
the example.

Because now the formType has some dependencies, you must define this formType as
a service in the dependency injection configuration.

``` yml
services:
    form_type.product:
        class: My\Namespace\Form\Type\ProductType
        calls:
            - [setEntityTranslatorFormEventListener, [@elcodi.entity_translator_form_event_listener]]
        tags:
            - { name: form.type, alias: form_type_product }
```

And that's it. Try to render your form, treating name and description like they
were normal fields and what you will see is that, instead of rendering a text
input field and a Textarea, three of each will be rendered.

## Custom form views

You can easily customize the way of rendering these fields by adding some format
into the rendering of `translatable_field`. You can find more information in
chapter - [What are Form Themes?](http://symfony.com/doc/current/cookbook/form/form_customization.html#what-are-form-themes)

## Automatic entity translation

You can enable the automatic translation of all loaded entities by enabling the
related configuration flag `auto_translate`.

``` yml
elcodi_entity_translation:
    auto_translate: true
```

By default this value is true. If true, every time you load a new entity from
Doctrine than is defined as translatable (a field is included in the
configuration), that entity will be translated using the current locale.
