Entity Translator
=================

Translate your entities in a very decoupled way.

* [Bundle Documentation](http://elcodi.io/docs/bundles/entity-translator/)
* [Github Repository](https://github.com/elcodi/EntityTranslator)

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
