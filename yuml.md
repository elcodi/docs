# Yuml graph definition

This page contains all this documentation uml definitions. Each one has the name
of the image stored locally and the definition for the website
[Yuml.me](http://yuml.me).

To build each image, append the value of the definition after
`http://yuml.me/diagram/scruffy/class/edit/`.

### manuscript/images/model/cart-component.png

[CartLine|cart;orderLine;purchasable]-1>[note: Purchasable Model{bg:cornsilk],  
[OrderLine|order;cartLine;purchasable]-1>[note: Purchasable Model{bg:cornsilk],  
[Cart|order;cartLines]1++-0..*>[CartLine],  
[Order|cart;orderLines]<1-0..*>[OrderLine],  
[Cart]<1-0..1>[Order],  
[CartLine]<1-0..1>[OrderLine]

![Cart component model](manuscript/images/model/cart-component.png)

### manuscript/images/model/cart-cartline.png

[CartLine|cart;orderLine;purchasable]-1>[note: Purchasable Model{bg:cornsilk],  
[Cart|order;cartLines]1++-0..*>[CartLine]

![Cart Cartline model](manuscript/images/model/cart-cartline.png)

### manuscript/images/model/order-orderline.png

[OrderLine|order;cartLine;purchasable]-1>[note: Purchasable Model{bg:cornsilk],  
[Order|cart;orderLines]<1-0..*>[OrderLine]

![Order Orderline model](manuscript/images/model/order-orderline.png)

### manuscript/images/model/product-component.png

[Variant|product;values],
[Value|attribute],
[Purchasable]^-[Product],
[Purchasable]^-[Variant],
[Product]1++-*>[Variant],
[Variant]<*-*>[Value],
[Attribute]<1-*>[Value]

![Product component model](manuscript/images/model/product-component.png)

### manuscript/images/model/category.png

[Category|subCategories;parent;products],
[Category]<1-*>[Category],
[Category]<*-*>[Product]

![Category model](manuscript/images/model/category.png)
