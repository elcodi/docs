# Elcodi Watermark

We need a small watermark for all Elcodi E-commerce implementation. This
watermark should be very soft, small and imperceptible by the human eye, only
accessible by developers and robots.

We want to introduce you a Symfony Bundle called
[HTTPHeadersBundle](https://github.com/mmoreram/HTTPHeadersBundle). This bundle
is very basic, only 2 PHP classes indeed. You can read there the documentation,
and we will explain how we are using this project.

## X-Elcodi

This is the Response HTTP Header for Elcodi. As you can see in the `config.yml`
file in Bamboo, the HTTPHeadersBundle configuration sets this header for all
site pages

``` yaml
#
# HTTP Headers
# Fill this data to ensure your ecommerce is unique and belongs to developers
# Checkout the configuration in https://github.com/mmoreram/HTTPHeadersBundle
#
http_headers:
    response:
        x_elcodi:
            name: X-Elcodi
            values:
                - This E-commerce is built using Elcodi and Symfony
```

Please, this header will allow some engines to detect your website as an
implementation of Elcodi. This means more installations, more development, and
this always means more confidence for other people.