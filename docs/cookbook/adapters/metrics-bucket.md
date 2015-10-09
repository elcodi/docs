How to add a new Metric Bucket adapter
======================================

By default, Elcodi provides one Metric Bucket implementation, based on Redis.
You can read more information about this in the
[Book Metrics Chapter](../../book/metrics.md). This feature has been implemented
using Adapters as well, so you could implement your own.

## Scenario

You have your store installed in third party environment. Bad news... Redis is
not installed and you cannot install it even if you want it. This scenario is
very common, so you should be able to use Metrics as well, even without Redis.

## Solution

Well, first of all you should think about where to place your metric cache. A
bucket is nothing but a cache layer for your metrics. Each metric entry is a
database entry as well, but because only working with Doctrine would be so hard
for your server and user, we work with a cache layer.

This cache implementation should be able to build results in a very fast way, so
this is the reason why default implementation uses Redis.

### AbstractMetricsBucket

Like every other adapters implementations, there is an Interface for that.
Remember that an abstract class can work as an interface as well if you define
methods as abstract.

AbstractMetricsBucket is your class if you want to create a new adapter for
that.

``` php
namespace Elcodi\Component\Metric\Core\Bucket\Abstracts;

use Elcodi\Component\Metric\Core\Entity\Interfaces\EntryInterface;

/**
 * Class AbstractMetricsBucket
 */
abstract class AbstractMetricsBucket
{
    /**
     * Add Metric into Bucket
     *
     * @param EntryInterface $entry Entry
     *
     * @return $this Self Object
     */
    abstract public function add(EntryInterface $entry);

    /**
     * Get number of unique beacons given an event and a set of dates
     *
     * @param string $token Event
     * @param string $event Token
     * @param array  $dates Dates
     *
     * @return integer Number of hits
     */
    abstract public function getBeaconsUnique($token, $event, array $dates);

    /**
     * Get the total of beacons given an event and a set of dates
     *
     * @param string $token Event
     * @param string $event Token
     * @param array  $dates Dates
     *
     * @return integer Number of beacons, given an event and dates
     */
    abstract public function getBeaconsTotal($token, $event, array $dates);

    /**
     * Get distributions given an event and a set of dates
     *
     * @param string $token Event
     * @param string $event Token
     * @param array  $dates Dates
     *
     * @return integer Accumulation of event and given dates
     */
    abstract public function getAccumulation($token, $event, array $dates);

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
    abstract public function getDistributions($token, $event, array $dates);
}
```

This class provides you some helpers as well, related to how cache keys should
be built in order to avoid collisions inside the bucket

``` php
/**
 * Create key given an entry
 *
 * @param string $token Event
 * @param string $event Token
 * @param string $date  Date
 *
 * @return string key
 */
protected function generateEntryKey($token, $event, $date)
{
    return
        $this->normalizeForKey($token) .
        '.' .
        $this->normalizeForKey($event) .
        '.' .
        $this->normalizeForKey($date);
}

/**
 * Normalize string for key format
 *
 * @param string $string String
 *
 * @return string String normalized
 */
public function normalizeForKey($string)
{
    return str_replace('.', '_', $string);
}
```

### Used Adapter

The adapter used by the Dependency Injection is the one aliased with the name
`elcodi.metrics_bucket`.

If you want to change the adapter, the only thing you must do is overwrite the
Metric Bundle configuration in your project, by adding this in your
`/app/config/config_local.yml` file.

``` yaml
elcodi_metric:
    bucket:
        client: elcodi.redis_metrics_bucket
```

### Adapters

These are our adapters included in the Core of the application. If you have
implemented another adapter and you think that can be interesting to share it,
then you can create a pull request with your work. We will appreciate it.

#### Redis Bucket Adapter

* Namespace - Elcodi\Component\Metric\Core\Bucket\RedisMetricsBucket
* DI name - `elcodi.redis_metrics_bucket`

This extension doesn't need any extra installation. If redis-server is not
installed, then nothing happens. If it is, then will work properly.
