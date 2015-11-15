# How to add a new Location Loader adapter

Elcodi uses by default the Github implementaton, retrieving data from some a
Github repository.

## Scenario

You are using another implementation for Locations. This means that your
database structure is not the same as Elcodi proposes. Then, if you still want
to use this feature for fast location loading, then you must implement your own
loader adapter.

## Solution

For your own adapter implementation you only need to know where your actual data
is stored. The adapter itself is only a class that implements interface
`Elcodi\Component\Geo\Adapter\LocationLoader\GithubLocationLoaderAdapter`.

``` php
namespace Elcodi\Component\Geo\Adapter\LocationLoader\Interfaces;

/**
 * Interface LocationLoaderAdapterInterface
 */
interface LocationLoaderAdapterInterface
{
    /**
     * Given a country name, return the sql to be loaded
     *
     * @param string $countryName Country name
     *
     * @return string Sql to be loaded
     */
    public function getSqlForCountry($countryName);
}
```

## Used Adapter

The adapter used by the Dependency Injection is the one aliased with the name
`elcodi.location_loader`.

If you want to change the adapter, the only thing you must do is overwrite the
Geo Bundle configuration in your project, by adding this in your
`/app/config/config_local.yml` file.

``` yaml
elcodi_geo:
    location:
        loader_adapter: elcodi.location_loader_adapter.github
```

## Adapters

These are our adapters included in the Core of the application. If you have
implemented another adapter and you think that can be interesting to share it,
then you can create a pull request with your work. We will appreciate it.

### Github Loader Adapter

Uses Github repository
[elcodi/LocationDumps](https://github.com/elcodi/LocationDumps), and actually
only some countries have been pre-generated.

* Namespace - Elcodi\Component\Geo\Adapter\LocationLoader\GithubLocationLoaderAdapter
* DI name - `elcodi.location_loader_adapter.github`

Countries generated:
* [Andorra - AD](https://raw.githubusercontent.com/elcodi/LocationDumps/master/AD.sql)
* [Austria - AT](https://raw.githubusercontent.com/elcodi/LocationDumps/master/AT.sql)
* [Belgium - BE](https://raw.githubusercontent.com/elcodi/LocationDumps/master/BE.sql)
* [Switzerland - CH](https://raw.githubusercontent.com/elcodi/LocationDumps/master/CH.sql)
* [Germany - AD](https://raw.githubusercontent.com/elcodi/LocationDumps/master/DE.sql)
* [Denmark - DK](https://raw.githubusercontent.com/elcodi/LocationDumps/master/DK.sql)
* [Spain - ES](https://raw.githubusercontent.com/elcodi/LocationDumps/master/ES.sql)
* [Finland - FI](https://raw.githubusercontent.com/elcodi/LocationDumps/master/FI.sql)
* [France - FR](https://raw.githubusercontent.com/elcodi/LocationDumps/master/FR.sql)
* [United Kingdom - GB](https://raw.githubusercontent.com/elcodi/LocationDumps/master/GB.sql)
* [Italy - IT](https://raw.githubusercontent.com/elcodi/LocationDumps/master/IT.sql)
* [Poland - PL](https://raw.githubusercontent.com/elcodi/LocationDumps/master/PL.sql)

If you generate your country and is not in this list or is not updated, then
don't hesitate to create a PR and ping us
