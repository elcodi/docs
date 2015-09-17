# Category Architecture

Elcodi brings you a small and simple category structure. Small and simple
doesn't mean bad and useless, but with sufficient quality to be useful and 
scalable for any E-commerce.

![Category model](../image/model/category.png)

## Category tree

The main service of the component, and given that you have your categories built
properly, is the service Category Tree. This service is only intended to create
a basic structure of categories, ready to be saved in a plain way.

Imagine all steps in each Request without using this service.

* I want all categories
* I need to get them all from Database using Doctrine
* I need to sort them
* I need to filter them
* I need to organize them using the proper hierarchy

While there are no changes in your database, these bunch of actions produce the
same result, so why we do it once and again? That's senseless.

This service will do this action only once and will save it in your cache
system in a very reduced and simplified way. Of course the service will not save
entities in cache but an array.

After that, each request will only require that structure saved in cache,
reducing this way the resources spend by the system.

## Flushing

As soon as your model changes, then you must flush your cache layer. At the
moment this happens only when category or product table changes.

* Of course, if category changes, then the cached item should be flushed in 
order to be rebuilt again.
* Because some categories may not appear because of no products are attached to
it, if the table products changes then the cached tree should be flushed as
well. There is a flag that allow categories to appear even if they don't have
products, so tree will be flushed only when this flag is disabled.

## Overwrite the service

Each project may add their own login about how categories should be stored.
That's a fact indeed. So, overwriting this service is as easier as overwriting
any other service. Please, follow these cookbooks.

* [Overwrite a service](../cookbook/overwrite/overwrite-a-service.md)