Attribute
=========

Use and configure the Attribute component in your Symfony project.

* [Component Documentation](http://elcodi.io/docs/components/entity-translator/)
* [Github Repository](https://github.com/elcodi/AttributeBundle)

## Usage

Because the extension of the bundle addresses mainly the mapping of the
two main entities Attribute and Value, then there is little to say here.
However, the extension configures automatically directors, factories,
repositories, and managers to do the heavy lifting.

Attributes as described in the documentation for the component address
the need for configurable distinguishable properties for a product.
You could ideally describe any product in terms of attributes, e.g.:

```
Shoes

    attribute: size
    values: [3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
    
    attribute: material
    values: [leather, plastic, exotic]
    
    attribute: technology
    values: [airFoam, advanceFoam, everlastGrip]
    
    attribute: manufacturer
    values: [Japan, Peru, USA, Spain]
    
    attribute: generation
    values: [first, second]
    
    attribute: year
    values: [2014, 2015, 2016]
    
    attribute: section
    values: [mall, smaller shop, airport, complex]
```

This is just a representation of how one can describe the product via
attributes. And it shows really that the attributes are more powerful
than to just use them to do selects on product pages. Attributes can
be used on the backend more powerfully, have family of attributes that
can apply to specific product families or variations. Attribute validation
is one of the key ideas for configurable products for instance.

The bundle and component lay the foundation only of what is possible.
All other things will go in your application.
