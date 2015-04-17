How to Implement a Repository
=============================

Extremely easy, as Elcodi repositories are exactly the same thing that how 
Symfony treat these classes.

Each entity has its own Repository. You will find all entities related 
repositories inside the Components, hosted in `repository/` folder.

All of them are mostly empty, so they extend `AbstractRepository` and, by 
default, have a set of accessible public methods, but some of them have custom
methods in order to build specific and complex queries.

If you want to learn about how to create these methods, please take a look at 
the [Symfony Documentation](http://symfony.com/doc/current/book/doctrine.html#custom-repository-classes)
for custom repository classes.