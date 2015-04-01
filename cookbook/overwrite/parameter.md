How to Overwrite a Parameter
============================

You can overwrite Elcodi by using the file `app/config/config_local.yml`. This
file is loaded at the end of the `app/config/config.yml` file, allowing final 
users to add custom elements, overriding all project definitions.

Why you should use `config_local.yml` file?

Well, this file will never be improved nor evolved, so even if you pull into 
your project last Elcodi versions, you will never have conflicts in that file.

Let's see an example

``` yaml
# app/config/config_local.yml
parameters:
    database_host: 10.0.0.1

elcodi_currency:
    currency:
        default_currency: EUR
        session_field_name: currency_id
```
