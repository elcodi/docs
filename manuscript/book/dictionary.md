# Dictionary

In this dictionary you will find some meanings of some of our most used words.
If there is one word you don't know what is about, please ask us in out
[Gitter channel](http://gitter.im/elcodi/elcodi) and we will create the entry
as soon as possible.

We will very happy to help you :)

### Attribute

Refers to a feature of a Variant.  
Please, check [Variant](#variant)

### Bamboo

Bamboo is a simple and basic E-commerce application based on Symfony, that uses
all Elcodi components for it's own business logic. All Elcodi related best 
practices are applied and used in this project.

### CartLine

Everytime a product or a variant is added into your cart, some information
related to this specific action must be stored. For example, if you add a
Variant and some extra information must be stored, where do you do it? Or where
do you store how many units of a product you want to buy?

If the relation between a Product and a Cart is persisted without any kind of
middle class, then you cannot store this information.

So, a CartLine is an object in the middle between any purchasable and a cart to
store information about this purchasable instance and the cart.

![Cart Cartline model](images/model-cart-cartline.png)

### Component

A component is a simple PHP Library. Only PHP Classes are placed inside this 
package and must be framework agnostic. Because this project is a Symfony
project, each component has it's own Bundle, where all libraries are exposed to
the Symfony Dependency Injection and where are bundles are created and
configured.

Elcodi has splitted its business logic in several components, each one 
containing specific parts of the model, and grouped semantically. CHeckout some
of them:

* [Cart Component](http://github.com/elcodi/Cart)
* [Product Component](http://github.com/elcodi/Product)
* [Core Component](http://github.com/elcodi/Core)

Some of our components are detailed and documented in this documentation.

* [Elcodi Components](http://elcodi.io/docs/component/)

And some other components are described as book chapters, describing a little
bit more its architecture and workflow.

* [Cart Architecture](http://elcodi.io/docs/book/cart-architecture/)
* [Product Architecture](http://elcodi.io/docs/book/product-architecture/)

### Factory

PHP Class with one single responsibility: create a new entity instance. This
class should know as well how this entity instance must be initialized given
an specific environment

- My CustomerFactory initializes all my Customer new instances as females 
  because my project is focused on women.
- Other projects can overwrite this class by defining how a Customer should be
  initialized
- Because of that, the customer itself should never know about it's own
  initialization
  
To know a little bit more about factories, please follow this interesting links:

* [How to implement a factory](http://elcodi.io/docs/cookbook/implementation/implement-a-factory/)
* [Factory pattern in Symfony2](http://mmoreram.com/blog/2013/12/23/factory-pattern-in-symfony2/)

### Plugin

Because Bamboo must be a project with a well-designed extension point. For such
reason, and taking advantage of how Symfony Bundles work, we have created a 
plugin engine in order to allow people overwriting and extending Bamboo in a
very simple and intuitive way.

When we refer to a Plugin then, we refer to these Bundles that are thought as 
Plugins, following their specification.

There is a full book chapter about Elcodi plugins, and this part is going to be
improved day by day with new ideas, with new developments and with new
specifications.

[Elcodi Plugins](http://elcodi.io/docs/book/plugins/)

### Repository

Each entity has it's own repository. When we refer to a repository, we are
automatically referring to the Doctrine Repositories.

Each repository is created by default and defined inside the Dependency
Injection service map using the structure `elcodi.repository.{entity_name}`.

To know more about Repositories in Elcodi, please check these links.

[How to implement a Repository](http://elcodi.io/docs/cookbook/implementation/implement-a-repository/)
[Doctrine Repositories](http://doctrine-orm.readthedocs.org/en/latest/reference/working-with-objects.html)

### Value

Refers to a value of an attribute and assigned to a Variant.  
Please, check [Variant](#variant)

### Variant

Sometimes a Product is not enough when we need to define something we want to 
sell. Let's see an example.

* In our store we sell a T-shirt
* If your T-Shirt is only sold only in red color and you only have one size, 
  then that's good enough for you
* But what happens when your T-shirt is available in many colors or many sizes?
* Your product will still be the same T-shirt, but you need a new entity to say
  that people can buy this T-shirt in red, blue and grey, and in several sizes,
  like small, medium or large.
* When we talk about Variant, we talk about each available combination, 
  containing the original product, the color and the size.
* Of course, if your have several Variants of a specific Product, people will be
  able to add these variants to their carts, instead of the Product.

This is the main definition of what a Variant is, but in this case we will
introduce you as well the common terms *Attribute* and *Value*. Each variant
instance is a Product element combined to an Attribute and a Color.

Let's continue with the same example, but specifying a little bit.

* We have our T-shirt Product
* We have created a new Attribute called Size
* We have created three new values related to this Attribute, called Small,
Medium and Large. All three have Size as Attribute.
* We have created one new Variant, having our the T-shirt as product, and having
Small as Value. Because this Value has Size as Attribute, then we can say that:

We have a new Variant called "Small T-shirt"
* Product: T-shirt
* Attribute: Size
* Value: Small

Of course, a Variant is a *Purchasable* like Product, so you can add it into
your Cart.
