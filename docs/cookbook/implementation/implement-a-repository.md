# How to Implement a Repository

Creating a repository in Elcodi is easy. Elcodi type repositories are used in the same
way Symfony uses its repository classes.

## Scenario

We have created a new entity properly, and we are happy about that. We have a 
new service that must create a specific search against this new entity, and we
need some nice class to place this logic

What people need to create complicated search queries, they are used to placing
all this logic inside their services using the repository. Great, this can be a
good decision if you're not planning to test your application. If you want, then
you should use your repositories as are meant to be used: one repository per 
entity with all specific query logic.

## Solution

Each entity has its own Repository. You will find all entities related 
repositories inside the Components, hosted in `Repository/` folders.

All of them are mostly empty, because they extend `AbstractRepository` and, thereby
default, have a set of accessible public methods. However some of them have custom
methods in order to build specific and complex queries.

## Related Links

* [Symfony docs - Custom repository classes]http://symfony.com/doc/current/book/doctrine.html#custom-repository-classes)