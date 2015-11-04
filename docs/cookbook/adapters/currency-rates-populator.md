# How to add a new Currency Rates Populator adapter

Bamboo works with several currencies at the same time. This happens when your
shop work with different countries.

## Scenario

Because you have several currencies, but all products are defined using one
currency, you need to know the equivalence value between them all.

The point is that there are a lot of services that provide this information, and
maybe you want to use one specific, non implemented yet or even your own
currency rates server.

## Solution

You can create a new adapter for Currency Rates populator. By default, Elcodi
implementation works with the Yahoo Finance provider. This adapter can be easily
replaced by creating a new class implementing 
`CurrencyExchangeRatesProviderAdapterInterface`.

``` php
namespace Elcodi\Component\Currency\Adapter\CurrencyExchangeRatesProvider\Interfaces;

/**
 * Interface CurrencyExchangeRatesProviderAdapterInterface
 */
interface CurrencyExchangeRatesProviderAdapterInterface
{
    /**
     * Get the latest exchange rates.
     *
     * This method will take in account always that the base currency is USD,
     * and the result must complain this format.
     *
     * [
     *      "EUR" => "1,78342784",
     *      "YEN" => "0,67438268",
     *      ...
     * ]
     *
     * @return array exchange rates
     */
    public function getExchangeRates();
}
```

Then, you need to define your class as a service.

``` yml
services:

    #
    # My currency rates adapter
    #
    my_currency_rates_adapter:
        class: My\Currency\Rates\Adapter
```

And finally you need to expose your adapter implementation as the used by the 
framework when loading your currency rates. You can do that by configuring the
CurrencyBundle and setting your new service id.

``` yml
elcodi_currency:
    rates_provider:
        client: my_currency_rates_adapter
```

Now you can use your new implementation by populating your currencies through
the command line.

``` bash
$ php app/console elcodi:exchangerates:populate
```

## Used Adapter

The adapter used by the Dependency Injection is the one aliased with the name
`elcodi.currency_exchange_rate_adapter`.

If you want to change the adapter, the only thing you must do is overwrite the
Currency Bundle configuration in your project, by adding this in your
`/app/config/config_local.yml` file.

``` yaml
elcodi_currency:
    rates_provider:
        adapter: elcodi.currency_exchange_rate_adapter.yahoo_finances
```

## Adapters

These are our adapters included in the Core of the application. If you have 
implemented another adapter and you think that can be interesting to share it,
then you can create a pull request with your work. We will appreciate it.

### Yahoo Finances Adapter

* Namespace - Elcodi\Component\Currency\Adapter\CurrencyExchangeRatesProvider\YahooFinanceProviderAdapter
* DI name - `elcodi.currency_exchange_rate_adapter.yahoo_finances`

This extension doesn't need any extra installation
