# Translate Elcodi

Elcodi is a worldwide project, and should be understandable by any interested
developer or final user. Because of that, we want to care about all languages
and translations.

But how you can do it?

## Translation Server

Elcodi works with a project called
[Translation Server](https://github.com/mmoreram/translation-server). This
project provides you a small set of console commands that will enable you to
check better how complete is translations for each defined language.

Check the documentation of that project in the same repository.

## Listing resources

First of all you should check all Elcodi's translation resources. In these
resources we include translations from our Bamboo project, including both Admin
panel and our default Front template. We actually include some of our default
Plugins.

To have the complete list of all these resources, you can use the console.

``` bash
$ php app/console translation:list-resources

Directories list :
    - %kernel.root_dir%/../src/Elcodi/Admin/CoreBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Store/CoreBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Common/CommonBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/GoogleAnalyticsBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/PinterestBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/ProductCsvBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/StoreSetupWizardBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/DisqusBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/TwitterBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/FacebookBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/StoreTemplateBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/PaypalWebCheckoutBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/FreePaymentBundle/Resources/translations
    - %kernel.root_dir%/../src/Elcodi/Plugin/StripeBundle/Resources/translations
done!
```

## Languages

Elcodi works with a preset of languages. These languages are defined in a file
called [`.translation.yml`](https://github.com/elcodi/bamboo/blob/master/.translation.yml).
In this file, Elcodi defines the base of all languages we will work with. If
your language is not included, please send a Pull Request in order to add it as
soon as possible.

English is the main language, so the percentage of it should be complete.
You can check how translated is each language by using the translation-server
console command.

``` bash
$ bin/translation-server translation:server:view

[Trans Server] Command started at Mon, 02 Nov 2015 17:24:36 +0100
[Trans Server] Translations for [en] is 100% completed. 0 missing
[Trans Server] Translations for [de] is 99.57% completed. 4 missing
[Trans Server] Translations for [ca] is 99.57% completed. 4 missing
[Trans Server] Translations for [es] is 99.57% completed. 4 missing
[Trans Server] Translations for [fr] is 78.36% completed. 203 missing
[Trans Server] Translations for [it] is 76.44% completed. 221 missing
[Trans Server] Translations for [fi] is 76.23% completed. 223 missing
[Trans Server] Translations for [nl] is 0% completed. 938 missing
[Trans Server] Translations for [eo] is 0% completed. 938 missing
[Trans Server] Translations for [gl] is 0% completed. 938 missing
[Trans Server] Translations for [eu] is 0% completed. 938 missing
[Trans Server] Command finished in 1439 milliseconds
[Trans Server] Max memory used: 17301504 bytes

```

## Completing translations

Adding some translations to an existent language is so simple. Of course, as you
have done since now, you can check the file where the translation is missing
(can be so difficult or tricky) and add your translation. In Bamboo this process
will be much easier because all our translation files are sorted alphabetically,
but even that, maybe you only want to add some translations in your language, no
matter where the translation is placed.

In that case, we have a very simple way of adding *random* translations in your
language by using as well a translation-server console command.

``` bash
$ bin/translation-server translation:server:add

[Trans Server] Command started at Mon, 02 Nov 2015 17:43:42 +0100
[Trans Server] Language : fr
[Trans Server] Key : admin.identity.section.logo.description
[Trans Server] Original : Here you can add an image with your logo. Please use the maximum quality posible.
[Trans Server] Translation : 
```

To know a little bit more about all the possibilities this command brings you, 
read its [documentation](https://github.com/mmoreram/translation-server/blob/master/README.md#asking-for-new-translations)

## Adding translations

The easiest way of adding a new language from the scratch is by copying the
english translation files to your language. By using this method, you will only
have to overwrite the original value to the translated one.

Anyway, if your language is inserted as a Bamboo language in the
`.translation.yml` file, you can just use the previous command 
`translation:server:add` and forget about how internal files are disposed.