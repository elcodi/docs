# Metrics

Add a simple asynchronous metrics layer into your Symfony project.

* [Component Repository](https://github.com/elcodi/Metric)
* [Bundle Repository](https://github.com/elcodi/MetricBundle)

## Requirements

Metrics main adapter requires Redis to be installed. It uses some complex data 
structures to all metrics cache and read, and Redis offers by default this 
structures with a very fast reading time penalty.

It is required to install Redis in version, at least, `v2.8.9`. This version
introduced used methods like `HyperLogLog`.

If any other bucket adapter is installed and used, then you may not require
Redis but other libraries (each adapter should document it).

## What offers

This package provides a simple but powerful metrics layer, ready to be used in
your Symfony project, and inspired by how Google Analytics uses your client
resources.

The package is divided in three main blocks
* The core, where all the service layer is placed
* The input, where all the data is inserted in the system
* The API, where all the data is required and read from

When we talk about metrics, we are talking about some indicators of how our site
is working. In order to provide the final user with some useful data, this 
component allows you to store metrics in a very different way. Let's see all
these ways using examples.

## Unique beacon

For example, unique users. This type of metric allow you to get unique elements
given an identifiable per each actor. Each entry represents only one increment.

``` php
/**
 * Metric type beacon unique.
 *
 * This type will be treated as a simple beacon in the metric ecosystem.
 * Useful for getting nb of elements
 *
 * * To get unique values over the time
 */
const TYPE_BEACON_UNIQUE = 1;
```

The usage is quite simple. If you need to add this beacon in your PHP classes,
then use the `MetricManager` instance, injectable in your classes by using the
service called `elcodi.manager.metric`.

In this example, we want to add a new metric called `homepage_controller`,
we want to track how many unique users visit this controller (imagine that this
controller is behind a firewall that require all users to have credentials), and
that the store itself has its own tracker, no matter what's the value.

``` php
use Elcodi\Component\Metric\ElcodiMetricTypes;

$user = ...;
$storeTracker = ...;
$this
    ->metricManager
    ->addEntry(
        $storeTracker,
        'homepage_controller',
        $user->getId(),
        ElcodiMetricTypes::TYPE_BEACON_UNIQUE,
        new DateTime()
    );
```

As you can see, in that case we are not tracking how many users render the final
twig, but how many users use the controller.

If our desire is tracking the page view, then you should use the Javascript
library, designed and created for that purpose. Read the
[#initializing-metrics](Initializing metrics) section

``` jinja
_etc.push(["123456789", 'homepage', {{user.id}}, '1']);
```

In Bamboo, the identifier of the store is available by using the Twig global 
variable `store_tracker`.

``` jinja
_etc.push(["{{ store_tracker }}", 'homepage', {{user.id}}, '1']);
```

## Total beacon

Same as unique beacons, but each beacon represents a new entry, no matter what
the user where this beacon comes from, each one means a new entry. Useful for
total page requests. Each entry represents only one increment.

In this example, we want to do the same as before but no matter what user the
visit comes from.

``` php
use Elcodi\Component\Metric\ElcodiMetricTypes;

$user = ...;
$storeTracker = ...;
$this
    ->metricManager
    ->addEntry(
        $storeTracker,
        'homepage_controller',
        '',
        ElcodiMetricTypes::TYPE_BEACON_TOTAL,
        new DateTime()
    );
```

If you want the same behavior but in your twig layer


``` jinja
_etc.push(["123456789", 'homepage', '', '2']);
```

## Mixed (Unique and Total) beacon

Sometimes you want to add both, unique and total beacons. Well, instead of
creating two different beacons, you can create  only one representing both.
Because this means that the user must be identified somehow for the unique
entry, remember to add the unique value as well.

``` php
use Elcodi\Component\Metric\ElcodiMetricTypes;

$user = ...;
$storeTracker = ...;
$this
    ->metricManager
    ->addEntry(
        $storeTracker,
        'homepage_controller',
        $user->getId(),
        ElcodiMetricTypes::TYPE_BEACON_ALL,
        new DateTime()
    );
```

And for the twig layer

``` jinja
_etc.push(["123456789", 'homepage', {{user.id}}, '3']);
```

## Accumulated beacon

This type allow you to create an incremental accumulator, for example for
representing the total value of your total earnings. In that case, we need the
amount of the order as integer.

``` php
use Elcodi\Component\Metric\ElcodiMetricTypes;

$orderAmount = ...;
$storeTracker = ...;
$this
    ->metricManager
    ->addEntry(
        $storeTracker,
        'order_earnings',
        $orderAmount,
        ElcodiMetricTypes::TYPE_ACCUMULATED,
        new DateTime()
    );
```

This type of metric is special for the PHP layer, so all entries must define a
value. Anyway, you can use it as well in your twig layer.

``` jinja
_etc.push(["123456789", 'metric', 10, '4']);
```

## Distributive beacon

Given an undetermined set of values, for examples, the referrer of the visit,
create a metric

Given a set of supplementary values, for example operating systems (you cannot
use two of them at the same time), this type of beacon allow you to build
distributive metrics using a set of possible values. Each entry represents only
one increment.

In our example we will check if the user visiting a controller is logged or not.

``` php
use Elcodi\Component\Metric\ElcodiMetricTypes;

$isLogged = ...;
$storeTracker = ...;
$metricName = $isLogged
    ? 'framework_is_logged'
    : 'framework_is_not_logged';

$this
    ->metricManager
    ->addEntry(
        $storeTracker,
        $metricName,
        '0',
        ElcodiMetricTypes::TYPE_DISTRIBUTIVE,
        new DateTime()
    );
```

Another example, and using it in the twig layer, is the representation if all
referrers of your site. You must use this type of metric because you cannot
define a set of possible values (that should be a list of all websites in the
world... insane!)

> This type of metric can be misunderstood. Only use it for non-closed set of
> elements. Otherwise, for example for knowing if your users are visiting your
> site using a mobile platform or a desktop platform, use the first metric type.

So let's see the example of the referrers. We will use the twig method
`referrer_domain()` for such information. Visit
[Use Referrer Domain](http://elcodi.io/docs/cookbook/usage/referrer-domain/) cookbook for more
information.

``` jinja
_etc.push(["123456789", 'referrer', {{ referrer_domain() }}, '8'])
```

## Metrics routes

Once we know all different type of metrics, we should know as well how to
initialize all the environment. One of this things we must ensure that are
active is the route that we will use for our asynchronous calls.

``` yaml
elcodi_metric:
    resource: "@ElcodiMetricBundle/Resources/config/routing.yml"
```

As you can see, MetricBundle provides you the file with these routes. You only
need to include it in your router.

As soon as is included, you should see the route enabled in your project by
using the command `php app/console router:debug`

``` bash
# elcodi.route.metric_input
RewriteCond %{REQUEST_URI} ^/api/_m/([^/]++)/([^/\.]++)\.png$
RewriteCond %{REQUEST_METHOD} !^(GET|HEAD)$ [NC]
RewriteRule .* - [S=1,E=_ROUTING_allow_GET:1,E=_ROUTING_allow_HEAD:1]
RewriteCond %{REQUEST_URI} ^/api/_m/([^/]++)/([^/\.]++)\.png$
```

As you can see, this route starts with the prefix `/api`. This is because in
Bamboo this route is considered an Api route, and is prefixed with `/api`.

## JS initialization

You must initialize the JS layer as well. For this chapter, please be sure that
your assets are properly installed and dumped.

``` bash
php app/console assets:install
php app/console assetic:dump
```

First of all you must initialize the JS metric library. Put this piece of code
in your layout header. That simple!

``` js
<script>
    
    /**
     * Tracker generator for elcodi bamboo store
     */
    var _etc = _etc || [];
    
    (function() {
        var _etracker = document.createElement('script');
        _etracker.type = 'text/javascript';
        _etracker.async = true;
        _etracker.src = '/bundles/elcodimetric/js/tracker.js';
        var _etracker_s = document.getElementsByTagName('script')[0];
        _etracker_s.parentNode.insertBefore(_etracker, _etracker_s);
    }());
</script>
```

In simple words, what this piece of code does is create a new block of JS code
at the end of your code, adding all the metric library asynchronously. This
means none blocking at all.

Take a look at the first line of the code.

``` js
var _etc = _etc || [];
```

With this line you will be able to work with metrics even if the library is not
initialized yet. Let's see how!

## Adding beacons

Remember all beacons definitions? Well, if you have an idea about what your
first metric could be, let's create it.

For this example, we will use the referrer metric again. Let's remember how we
can use it.

``` jinja
_etc.push(["123456789", 'referrer', {{ referrer_domain() }}, '8'])
```

The object `_etc` is an array while the metric JS library is not installed.
Arrays in JS have a public method called `push()` that basically what it does
is pushing the element. That simple. This call does nothing, just pushing.

As long as the metrics library is installed, one of the processes this JS code
does is, just at the beginning of its execution, take this array, save the
objects inside, and create a new object in the same variable, with the same name
and the same public method: `push()`.

After that, introduces again the previously saved objects and, unlike the simple
JS array, this object does something else when this method is called: the AJAX
call to the metrics API.

And everything in a very very soft way and without any page slowness for your
user.

## Consulting values

Okay, now our website tracks some values for your analysis. But how can we read
all this data from our backend? Well, the component itself uses a set of
services with a very simple way of doing that job.

``` php
namespace Elcodi\Component\Metric\Core\Bucket\Abstracts;

/**
 * Class AbstractMetricsBucket
 */
abstract class AbstractMetricsBucket
{
    /**
     * Get number of unique beacons given an event and a set of dates
     *
     * @param string $token Event
     * @param string $event Token
     * @param array  $dates Dates
     *
     * @return integer Number of hits
     */
    public function getBeaconsUnique($token, $event, array $dates);
    
    /**
     * Get the total of beacons given an event and a set of dates
     *
     * @param string $token Event
     * @param string $event Token
     * @param array  $dates Dates
     *
     * @return integer Number of beacons, given an event and dates
     */
    public function getBeaconsTotal($token, $event, array $dates);
    
    /**
     * Get distributions given an event and a set of dates
     *
     * @param string $token Event
     * @param string $event Token
     * @param array  $dates Dates
     *
     * @return integer Accumulation of event and given dates
     */
    public function getAccumulation($token, $event, array $dates);
    
    /**
     * Get distributions given an event and a set of dates
     *
     * [
     *      "value3": 24,
     *      "value7": 13,
     *      "value8": 9,
     * ]
     *
     * @param string $token Event
     * @param string $event Token
     * @param array  $dates Dates
     *
     * @return array Distribution with totals
     */
    public function getDistributions($token, $event, array $dates);
}
```

You can inject this service by using the dependency injection name

``` yaml
services:
    my_service:
        class: My\Service\Class;
        arguments:
            - @elcodi.metrics_bucket
```

You can create new adapters as well. Please read the
[Metric Adapters Cookbook](http://elcodi.io/docs/cookbook/adapters/metric-bucket/)

## Twig extension

You can read this information as well in your twig layer. In that case, you
should have stored in a twig local variable the array of Datetime instances. In
that case, let's figure that we have this array in a variable called `dates`.

``` jinja
{{ metric_beacon_unique(123456789, 'unique_visits', dates) }}
{{ metric_beacon_total(123456789, 'total_visits', dates) }}
{{ metric_accumulation(123456789, 'order_total', dates) }}
{{ metric_distributive(123456789, 'referrers', dates) }}
```

All methods are a simple call to service methods, without any kind of
transformation.

## Metrics Intervals Resolver

The last parameter of all these methods is an array of dates, and because can be
a heavy task creating intervals of dates, Bamboo provides as well a simple
mechanism for building these structures easily.

> This service is just a helper for Bamboo installation, providing some extra
> configuration and definition for custom charts. If you want to use it for your
> own project, please, check the specification of the classes involved.

There is a class called `MetricIntervalsResolver` with a simple method. 

``` php
namespace Elcodi\Admin\MetricBundle\Services;

/**
 * Class MetricIntervalsResolver
 */
class MetricIntervalsResolver
{
    /**
     * Given a type, create and configure the interval container
     *
     * @param integer $type Type of calculation
     *
     * @return IntervalContainer
     */
    public function getIntervalContainer($type);
}
```

What this class does for you is create an instance of `IntervalContainer` built
with an array of DateTime instances, given given `$type`. This type will tell
the method what kind of interval you want to build. These are all pre-built
types.

``` php
namespace Elcodi\Admin\MetricBundle;

/**
 * Class AdminPanelTypes
 */
class AdminPanelTypes
{
    /**
     * @param integer
     *
     * Panel type today
     */
    const PANEL_TYPE_TODAY = 1;
    
    /**
     * @param integer
     *
     * Panel type yesterday
     */
    const PANEL_TYPE_YESTERDAY = 2;
    
    /**
     * @param integer
     *
     * Panel type last week
     */
    const PANEL_TYPE_LAST_WEEK = 3;
    
    /**
     * @param integer
     *
     * Panel type last month
     */
    const PANEL_TYPE_LAST_MONTH = 4;
    
    /**
     * @param integer
     *
     * Panel type last quarter
     */
    const PANEL_TYPE_LAST_QUARTER = 5;
}
```

As said before, this method returns a `IntervalContainer` instance. This object
returns some data related to the chart itself.

The most important and used method of this object is `getElements()`, used if
the value of `dates` is an instance of that. Then, the call of any metric read
turns like that

``` jinja
{{ metric_beacon_unique(123456789, 'unique_visits', dates.getElements()) }}
```

You can inject this resolver as well using the dependency injection name.

``` yaml
services:
    my_service:
        class: My\Service\Class;
        arguments:
            - @elcodi_admin.metric_intervals_resolver
```

And finally, you can access to this method in your Twig layer as well

``` jinja
{% set todayIntervalContainer = metric_create_interval_container(1) %}
{{ metric_beacon_unique(123456789, 'unique_visits', todayIntervalContainer.getElements()) }}
```