# Using referrer domain

You would possible know the referrer of your site views, right? Well, Bamboo
store provides you a couple of tools for this.

## Referrer provider

in Elcodi Core component you will find the service with all business logic
related to this. A simple class that will allow you to know some information
about your referrers.

``` php
namespace Elcodi\Component\Core\Services;

/**
 * Class ReferrerProvider
 */
class ReferrerProvider
{
    /**
     * Get referrer
     *
     * @return string Referrer
     */
    public function getReferrer();
    
    /**
     * Get referrer
     *
     * @return string Referrer
     */
    public function getReferrerDomain();
    
    /**
     * Referrer is search engine
     *
     * @return boolean Is search engine
     */
    public function referrerIsSearchEngine();
}
```

You can inject this service by using the dependency injection service name.

``` yml
services:
    my_service:
        class: My\Service\Class;
        arguments:
            - @elcodi.provider.referrer
```

## Referrer Twig extension

If you need to access to this information in the Twig layer, then there is an
Extension for that.

``` jinja
{{ referrer() }}
{{ referrer_domain() }}
{{ referrer_is_search_engine() }}
```