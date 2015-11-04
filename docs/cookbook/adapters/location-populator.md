# How to add a new Location Populator adapter

By default Elcodi uses an external website called
[Geonames](http://www.geonames.org/). This website proposes some data files with
country location references. This data is treated, organized and inserted as
entities in our Doctrine infrastructure.

This adapter just takes this data from that service and populates that country.

## Scenario

You have your own location server, for example, a non-free service that proposes
you this structure in a more precise way.

## Solution

You can create your own adapter for that. You just need to implement a simple
Interface, with one single method. That easy.

``` php
namespace Elcodi\Component\Geo\Adapter\LocationPopulator\Interfaces;

use Elcodi\Component\Geo\Entity\Interfaces\LocationInterface;

/**
 * Interface PopulatorInterface
 */
interface LocationPopulatorAdapterInterface
{
    /**
     * Populate a country
     *
     * @param string $countryCode Country Code
     *
     * @return LocationInterface Root location
     */
    public function populate($countryCode);
}
```

## Used Adapter

The adapter used by the Dependency Injection is the one aliased with the name
`elcodi.location_populator`.

If you want to change the adapter, the only thing you must do is overwrite the
Geo Bundle configuration in your project, by adding this in your
`/app/config/config_local.yml` file.

``` yaml
elcodi_geo:
    location:
        populator_adapter: elcodi.location_populator_adapter.geonames
```

## Adapters

These are our adapters included in the Core of the application. If you have
implemented another adapter and you think that can be interesting to share it,
then you can create a pull request with your work. We will appreciate it.

### Geonames Populator Adapter

* Namespace - Elcodi\Component\Geo\Adapter\LocationPopulator\GeonamesLocationPopulatorAdapter
* DI name - `elcodi.location_populator_adapter.geonames`
