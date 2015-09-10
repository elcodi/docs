# Yuml graph definition

This page contains all this documentation uml definitions. Each one has the name
of the image stored locally and the definition for the website
[Yuml.me](http://yuml.me).

To build each image, append the value of the definition after
`http://yuml.me/diagram/scruffy/class/edit/`.

### docs/image/model/cart-component.png

[CartLine|cart;orderLine;purchasable]-1>[note: Purchasable{bg:cornsilk],  
[OrderLine|order;cartLine;purchasable]-1>[note: Purchasable{bg:cornsilk],  
[Cart|order;cartLines]1++-0..*>[CartLine],  
[Order|cart;orderLines]<1-0..*>[OrderLine],  
[Cart]<1-0..1>[Order],  
[CartLine]<1-0..1>[OrderLine]

![Cart component model](docs/image/model/cart-component.png)

### docs/image/model/cart-cartline.png

[CartLine|cart;orderLine;purchasable]-1>[note: Purchasable{bg:cornsilk],  
[Cart|order;cartLines]1++-0..*>[CartLine]

![Cart Cartline model](docs/image/model/cart-cartline.png)

### docs/image/model/order-orderline.png

[OrderLine|order;cartLine;purchasable]-1>[note: Purchasable{bg:cornsilk],  
[Order|cart;orderLines]<1-0..*>[OrderLine]

![Order Orderline model](docs/image/model/order-orderline.png)

### docs/image/model/product-component.png

[Variant|product;values],
[Value|attribute],
[Purchasable]^-[Product],
[Purchasable]^-[Variant],
[Product]1++-*>[Variant],
[Variant]<*-*>[Value],
[Attribute]<1-*>[Value]

![Product component model](docs/image/model/product-component.png)