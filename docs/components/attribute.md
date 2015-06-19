Attribute
=========

Create different product attributes and values.

* [Bundle Documentation](http://elcodi.io/docs/bundles/attribute/)
* [Github Repository](https://github.com/elcodi/Attribute)

## Usage

In defining your products you would often create items that distinguish 
themselves by a specific characteristic. If it is clothes you know there can be 
sizes, colors, types of packaging, etc. You can have for instance used state for 
books. So you would have books that are used, new, as new, or refurbished, etc. 
In reality you would first come with the store and later your requirements will 
change or adapt. If a product needs any of such characteristic to be highlighted 
at the moment of purchase then we know that we are before a usage for the 
attribute component.

Because they can change rapidly, these attributes are not good to model in code
but they must be modeled via database entries as we see here.

Let's take the example of clothing sizes and define a size attribute with
three values as follows:

``` php
$attribute = $attributeDirector
    ->create()
    ->setName('Woman Clothing Sizes')
    ->setEnabled(true);

$attributeDirector->save($attribute);

foreach (['Small', 'Medium', 'Large'] as $size) {
    $value = $attributeValueDirector
        ->create()
        ->setValue($size)
        ->setAttribute($sizeAttribute);

    $attributeValueDirector->save($value);
}
```

From the structured data that we have created we can then handle these
clothing sizes to enrich the handling of products.

``` php
$sizes = $attributeRepository->findBy(['name' => 'Woman Clothing Sizes']);

// validate or select a size from the set ...
```

The component is tightly integrated with the bundle of the same name. The 
classes that the bundle uses, the services used in the example above, are in the 
component whereas the service definitions are declared in the bundle.

Apart from this, the component is mainly built from the Attribute and the Value 
objects. They relate together bidirectionally on its default mapping, though 
this can be overridden.

An Attribute bears a name and can find its value representations and Values are 
always mapping back to the Attribute for which they determine a representation.
An Attribute has temporal features meaning that the entity tracks time for when
it was created and when it is updated and also can be enabled or disabled.
The same is not true for any Value for that Attribute.

Example you can find out when the attribute Color was created or modified
but you cannot know when blue value was added or updated. Therefore, you
can find out when we got sizes for a product but not when we got sizes of
small or extra large for the products in the store.

Of course these limitations if needed can be overcame by simple overriding
the mapping.