Bundle Extensions
=================

The CoreBundle has general classes that enhance the developer experience to
reduce boilerplate, enforce the opinions of the framework, improve or ensure
functioning of the underlying Symfony framework and overall make life easier
working with the common needs of elcodi bundles.

```AbstractExtension``` is the base class that needs to be extended when
building your own bundle extension classes. This class takes care of the way
in which configuration is handled in elcodi framework.
Firstly the class require an extension name which is a snake case version of
the bundle for which the extension is created. This name is used to build the
root element of the configuration tree of the extension. And it will make
life easier to find a certain bundle configuration because elcodi
follows a common system-wide nomenclature for extension names as well.

``` php
// Bundle Extension Class

/**
 * @var string
 *
 * Extension name
 */
const EXTENSION_NAME = 'elcodi_attribute';


/**
 * Returns the extension alias, same value as extension name
 *
 * @return string The alias
 */
public function getAlias()
{
    return static::EXTENSION_NAME;
}
```

The ```AbstractConfiguration``` class is the base class for all bundle
configurations. Together with the ```AbstractExtension``` form the
foundation to enforce elcodi practices in bundles. It also has built-in the
implemented mapping configuration node for configuring the respective entity
mapping override. This configuration tree is always the same so there is
no need to specify it each time on your custom extension.

``` php
// Bundle Extension Class

/**
 * Return a new Configuration instance.
 *
 * If object returned by this method is an instance of
 * ConfigurationInterface, extension will use the Configuration to read all
 * bundle config definitions.
 *
 * Also will call getParametrizationValues method to load some config values
 * to internal parameters.
 *
 * @return ConfigurationInterface Configuration file
 */
protected function getConfigurationInstance()
{
    return new Configuration(static::EXTENSION_NAME);
}
```

The configuration class is precooked in the core bundles which already knows
the entities that are exposed to be overridden.

These two abstract classes above serve to generalize the configuration
loading in a more elegant object oriented way. You no longer have to
implement each method of the base Symfony interfaces for extensions but you
only have to return certain name values and arrays of values on a subset of
specific methods.

In addition to this things like the extension instance, the configuration
instance, and array of parameters are pushed back into the container
whether as object resources or in the parameter bag of the container.
These is very useful when doing the mapping override and resolving of
parameters as we will see later on.

Let's for example load configuration files!

One has to only list the names of the configuration files that the bundle
should load and they will be automatically loaded via a file locator one by
one. The only thing told to the extension class is the configuration files
location and which ones to load. If ```getConfigFiles``` returns an empty
list then nothing will be loaded.

``` php
// Bundle Extension Class

/**
 * Get the Config file location
 *
 * @return string Config file location
 */
public function getConfigFilesLocation()
{
    return __DIR__ . '/../Resources/config';
}

/**
 * Config files to load
 *
 * @param array $config Configuration array
 *
 * @return array Config files
 */
public function getConfigFiles(array $config)
{
    return [
        'factories',
        'repositories',
        'objectManagers',
        'directors',
    ];
}
```

Let's now override some mapping!

Overriding the mapping on the doctrine section of the container cannot
be done simply via a compiler pass or on the extension, especially
when we also want to use parametrized class names. Elcodi base classes
have already solved this for us using the prepend Symfony container
feature via implementing the ```PrependExtensionInterface``` interface.
This method allows us to override sections of other bundles and yet
keep the flexibility of using parameters that are resolved in the
container and that can be customized.

If we are to override mapping then our bundle extension should
implement the interface ```EntitiesOverridableExtensionInterface```
in addition to extending the ```AbstractExtension``` class. 
The method ```getEntitiesOverrides``` from the interface should return
a key value array where the key is the original interface and the value
is the fully qualified class name or parameter.
The ```AbstractExtension``` takes this array and prepends this
configuration into the container ```doctrine``` and ```elcodi_core```
sections thereby achieving the wanted dynamic mapping behavior.

``` php
// Bundle Extension Class

/**
 * Get entities overrides.
 *
 * Result must be an array with:
 * index: Original Interface
 * value: Parameter where class is defined.
 *
 * @return array Overrides definition
 */
public function getEntitiesOverrides()
{
    return [
        'Elcodi\Component\Attribute\Entity\Interfaces\AttributeInterface' => 'elcodi.entity.attribute.class',
        'Elcodi\Component\Attribute\Entity\Interfaces\ValueInterface' => 'elcodi.entity.attribute_value.class',
    ];
}

/**
 * Load Parametrization definition
 *
 * return array(
 *      'parameter1' => $config['parameter1'],
 *      'parameter2' => $config['parameter2'],
 *      ...
 * );
 *
 * @param array $config Bundles config values
 *
 * @return array Parametrization values
 */
protected function getParametrizationValues(array $config)
{
    return [
        "elcodi.entity.attribute.class" => $config['mapping']['attribute']['class'],
        "elcodi.entity.attribute.mapping_file" => $config['mapping']['attribute']['mapping_file'],
        "elcodi.entity.attribute.manager" => $config['mapping']['attribute']['manager'],
        "elcodi.entity.attribute.enabled" => $config['mapping']['attribute']['enabled'],

        "elcodi.entity.attribute_value.class" => $config['mapping']['value']['class'],
        "elcodi.entity.attribute_value.mapping_file" => $config['mapping']['value']['mapping_file'],
        "elcodi.entity.attribute_value.manager" => $config['mapping']['value']['manager'],
        "elcodi.entity.attribute_value.enabled" => $config['mapping']['value']['enabled'],
    ];
}
```

As you can see in the last method we can also provide parameterizable values
from our configuration and plug them to be used during the mapping override.

If you are still unsure of how to use these base classes. Don't worry just
take a peek at the extensions defined for instance in bamboo project. These
classes are even simpler because they don't need to do any mapping override
for the core bundles anymore since this is already done. So they become
places where we just hook configuration files that usually just have service
definitions for our glue services.

``` php
namespace Elcodi\Admin\AttributeBundle\DependencyInjection;

use Elcodi\Bundle\CoreBundle\DependencyInjection\Abstracts\AbstractExtension;

/**
 * Class AdminAttributeExtension
 */
class AdminAttributeExtension extends AbstractExtension
{
/**
 * @var string
 *
 * Extension name
 */
const EXTENSION_NAME = 'admin_attribute';

/**
 * Get the Config file location
 *
 * @return string Config file location
 */
public function getConfigFilesLocation()
{
    return __DIR__ . '/../Resources/config';
}

/**
 * Config files to load
 *
 * return array(
 *      'file1.yml',
 *      'file2.yml',
 *      ...
 * );
 *
 * @param array $config Config
 *
 * @return array Config files
 */
public function getConfigFiles(array $config)
{
    return [
        'formTypes',
    ];
}

/**
 * Returns the extension alias, same value as extension name
 *
 * @return string The alias
 */
public function getAlias()
{
    return self::EXTENSION_NAME;
}
}
```
