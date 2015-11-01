# How to add a new Location Provider adapter

One of the most important parts in an Ecommerce is the way Locations and all
Geo information is treated. In order to simplify the way Elcodi offers this
feature, you will be able to work with a nice Location structure using two
different accessing approaches.

## Scenario

Your Location structure has changed, or the way you access to the data. Maybe
this scenario is not common at all, because Elcodi covers the most important and
used ways of accessing, but you have your own particular way.

## Solution

Implementing an interface is the way you will be able to expose your way and not
breaking any external implementation. You'll see that implementable interface
offers some accessible methods, all of them specified properly in order to make
implementation easy to understand. This interface as well uses complex value
objects, so there's no margin of error there.

``` php
namespace Elcodi\Component\Geo\Adapter\LocationProvider\Interfaces;

use Elcodi\Component\Geo\ValueObject\LocationData;

/**
 * Interface LocationProviderAdapterInterface
 */
interface LocationProviderAdapterInterface
{
    /**
     * Get all the root locations.
     *
     * @return LocationData[] Collection of locations
     */
    public function getRootLocations();

    /**
     * Get the children given a location id.
     *
     * @param string $id The location Id.
     *
     * @return LocationData[] Collection of locations
     */
    public function getChildren($id);

    /**
     * Get the parents given a location id.
     *
     * @param string $id The location Id.
     *
     * @return LocationData[] Collection of locations
     */
    public function getParents($id);

    /**
     * Get the full location info given it's id.
     *
     * @param string $id The location id.
     *
     * @return LocationData Location info
     */
    public function getLocation($id);

    /**
     * Get the hierarchy given a location sorted from root to the given
     * location.
     *
     * @param string $id The location id.
     *
     * @return LocationData[] Collection of locations
     */
    public function getHierarchy($id);

    /**
     * Checks if the first received id is contained between the rest of ids
     * received as second parameter
     *
     * @param string $id  The location Id
     * @param array  $ids The location Ids
     *
     * @return boolean Location is container
     */
    public function in($id, array $ids);
}
```

### Used Adapter

The adapter used by the Dependency Injection is the one aliased with the name
`elcodi.location_provider`.

If you want to change the adapter, the only thing you must do is overwrite the
Geo Bundle configuration in your project, by adding this in your
`/app/config/config_local.yml` file.

``` yaml
elcodi_geo:
    location:
        provider_adapter: elcodi.location_provider_adapter.service
```

### Adapters

These are our adapters included in the Core of the application. If you have
implemented another adapter and you think that can be interesting to share it,
then you can create a pull request with your work. We will appreciate it.

#### Service Provider Adapter

This adapter uses a single service to make calls. This is useful for centralized
systems. Easy and simple.

* Namespace - Elcodi\Component\Geo\Adapter\LocationProvider\LocationServiceProviderAdapter
* DI name - `lcodi.location_provider_adapter.service`

#### API Provider Adapter

This adapter offers the same data but using a simple API infrastructure. This is
useful, for example, if you work with several projects at the same time, so you
only need one location database, shared by all websites.

* Namespace - Elcodi\Component\Geo\Adapter\LocationProvider\LocationApiProviderAdapter
* DI name - `lcodi.location_provider_adapter.api`

You can actually use an external location host. By default, this value is null,
so make sure you define properly this value in your
`/app/config/config_local.yml` file.

``` yaml
elcodi_geo:
    location:
        api_host: ~
```
