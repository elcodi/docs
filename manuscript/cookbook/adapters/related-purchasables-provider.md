# How to add a new Related Products Provider adapter

In some pages, specially all related to a purchasable object, you may show some
related purchasable objects as well. This features is very used in all websites
to increase the valuable relation between final pages in the same website.

## Scenario

Elcodi offers a simple implementation of how these purchasable objects should be
found, taking the principal category as the common element between them all. The
main thing here is that maybe you work with external implementations or services
that makes this task.

## Solution

Again, create your own adapter for that. Elcodi provides an interface for all
external implementations (our implementation uses this interface as well).

``` php
namespace Elcodi\Component\Product\Adapter\SimilarPurchasablesProvider\Interfaces;

use Elcodi\Component\Product\Entity\Interfaces\PurchasableInterface;

/**
 * Interface RelatedPurchasablesProviderInterface
 */
interface RelatedPurchasablesProviderInterface
{
    /**
     * Given a Purchasable, return a collection of related purchasables.
     *
     * @param PurchasableInterface $purchasable Purchasable
     * @param int                  $limit       Limit of elements retrieved
     *
     * @return array Related products
     */
    public function getRelatedPurchasables(PurchasableInterface $purchasable, $limit);

    /**
     * Given a Collection of Purchasables, return a collection of related
     * purchasables.
     *
     * @param PurchasableInterface[] $purchasables Purchasable
     * @param int                    $limit        Limit of elements retrieved
     *
     * @return array Related products
     */
    public function getRelatedPurchasablesFromArray(array $purchasables, $limit);
}
```

As you can see, all adapters must implement two methods. This is because
sometimes both methods require different implementations. Anyway, most
implementations will have common logic shared by both methods.

## Used Adapter

The adapter used by the Dependency Injection is the one aliased with the name
`elcodi.related_purchasables_provider`.

If you want to change the adapter, the only thing you must do is overwrite the
Product Bundle configuration in your project, by adding this in your
`/app/config/config_local.yml` file.

``` yaml
elcodi_product:
    related_purchasables_provider:
        adapter: elcodi.related_purchasables_provider.same_category
```

## Adapters

These are our adapters included in the Core of the application. If you have
implemented another adapter and you think that can be interesting to share it,
then you can create a pull request with your work. We will appreciate it.

### Same Category Related

Takes in account the same principal category of the given Purchasable. If the
object is a Variant, then its Product principal category is used. This
implementation not supports external Purchasable implementations.

* Namespace - Elcodi\Component\Product\Adapter\SimilarPurchasablesProvider\SameCategoryRelatedPurchasableProvider
* DI name - `elcodi.related_purchasables_provider.same_category`
