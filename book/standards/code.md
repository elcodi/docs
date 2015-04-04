Coding Standards
================

We are noticed that everyone has a different way of developing. In our team, 
some people place the braces at the end of the class definition, while other
people place them in a new line.

Is anyone wrong? Absolutely not, even PHP have some standards since a long time
ago, anyone should be able to work the way he wants.

But in Elcodi we have some standards for our code

### Small classes and methods

Each class should be enough small to contain specific information. Remember to
use small methods (no more than 20 lines) and to take care of the complexity of
them. The more complex is a method, the more hard is to understand what is all
about

### PHPDoc

This is an important part of the project.

All methods must be explained for humans. Some people think that the code should
be self-explainable, and we agree on that. This doesn't mean that all people 
should read code to understand what a method is intended for.

Pretending them to understand our method by its code is to prevent them to focus 
on their own business logic, and can hinder the development speed.

All methods must follow these rules as well, related to PHP Doc format

``` php
/**
 * This is my detailed description about what my code is intended for
 * I can use as many lines as possible.
 * I repeat, as many lines as possible.
 *
 * @param MyObject $myObject My object description
 * @param string   $info     Some information
 * @param integer  $number   Some number
 *
 * @return Factory Description of the result
 *
 * @throws AnException Scenario when this exception is thrown
 */
```

Using this kind of blocks there are some tips we all need to take in account.

* There is a space between all blocks
* The order is that one. Description, Params, Return and Exceptions
* All params should be aligned as is shown in the example
* Never using @inheritBlock. Is not useful at all and makes so difficult the
comprehension of the code

## Tools

We provide you a way of converting your way of programming to ours. Why don't 
you use this tools?

### PHP-CS-Fixer

* (Github repository)[https://github.com/FriendsOfPHP/PHP-CS-Fixer]

This library analyzes all the PHP source and tries to fix coding standards 
issues, using a custom definition set in the root of the project.

``` php
<?php

return Symfony\CS\Config\Config::create()
    // use SYMFONY_LEVEL:
    ->level(Symfony\CS\FixerInterface::SYMFONY_LEVEL)
    // and extra fixers:
    ->fixers(array(
        'concat_with_spaces',
        'multiline_spaces_before_semicolon',
        'short_array_syntax',
        '-remove_lines_between_uses'
    ))
    ->finder(
        Symfony\CS\Finder\DefaultFinder::create()
            ->in('src/')
    )
;
```

This library is a dependency of the project, but only in development 
environment, so while you are developing, if you have already updated your 
composer dependencies, you can run the fixer as follows

``` bash
php bin/php-cs-fixer fix
```

Because the dependency is set using an specific version of the code, all 
developers will fix the code the same way.

### PHP-Formatter

* (Github repository)[https://github.com/mmoreram/php-formatter]

Another simple php formatter. This one just will format your class headers, 
setting the project one in your PHP classes, and will sort all use statements in
order to comply with the specification

This library is a dependency of the project, but only in development 
environment, so while you are developing, if you have already updated your 
composer dependencies, you can run the fixer as follows

``` bash
php bin/php-formatter formatter:use:sort src/
php bin/php-formatter formatter:headers:fix src/
```

Because the dependency is set using an specific version of the code, all 
developers will fix the code the same way.
