Packages Structure
===================

A component must be treated as a simple PHP library. This means that it must be
framework agnostic. At the beginning, Elcodi was a set of Bundles, divided by
concerns or boundaries, and implemented as a single symfony-framework Bundle.

This was because of different opinions, of course, like every single
implementation of Open Source, but we noticed that there was an opinion repeated
once and again by some users.

> This project is Framework dependent.

We thought that being a Symfony dependent project was a good idea, but we had to 
decide what to be dependent on. Finally, we decided that Elcodi had to be
coupled to Symfony components and Doctrine library, so we had to split between
simple PHP libraries (Components) and Symfony Framework adaptations of these
libraries (Bundles).

### Component Structure

As we have said before, a component is just a PHP library. Think about it, we could use the Cart
classes in our own project, far from Symfony Framework, and because we don't 
want all the Framework dependencies inside our `vendor/` folder, maybe we should
only require the classes and their dependencies.

This is what a component is. Just classes.

So inside a component you will find a simple categorization of objects. All 
components follow the same structure.

```
Cart
  |
  |- Entity
  |    |- Interfaces
  |    |    |- CartInterface.php
  |    |- Abstracts
  |    |    |- AbstractCart.php
  |    |- Cart.php
  |
  |- Factory
  |    |- CartFactory.php
  |
  |- Repository
  |    |- CartRepository.php
  |
  |- Services
  |    |- CartManager.php
  |
  |- Wrapper
  |    |- CartWrapper.php
  |
  |- Transformer
  |    |- CartOrderTransformer.php
  |
  |- Event
  |    |- OnOrderCreatedEvent.php
  |
  |- EventListener
  |    |- CartEventListener.php
  |
  |- EventDispatcher
  |    |- CartEventDispatcher.php
  |
  |- Controller
  |    |- ImageUploadController.php
  |
  |- Command
       |- SitemapProfileCommand.php
```

This is just a sample of how a Component is structured, and how you should 
structure your project if you want to follow our way of doing things.

> This does not mean that this one is the best way of structuring your code, or
> does not mean that there is not another way of doing it. Is just our
> implementation and the one we suggest you.