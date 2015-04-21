How to Implement a Repository
=============================

Creating a repository in Elcodi is easy. Elcodi type repositories are used in the same
way Symfony uses its repository classes.

Each entity has its own Repository. You will find all entities related 
repositories inside the Components, hosted in `Repository/` folders.

All of them are mostly empty, because they extend `AbstractRepository` and, thereby
default, have a set of accessible public methods. However some of them have custom
methods in order to build specific and complex queries.

If you want to learn about how to create these methods, please take a look at 
the [Symfony Documentation](http://symfony.com/doc/current/book/doctrine.html#custom-repository-classes)
for custom repository classes.