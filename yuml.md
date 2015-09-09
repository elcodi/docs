# Yuml graph definition

This page contains all this documentation uml definitions. Each one has the name
of the image stored locally and the definition for the website
[Yuml.me](http://yuml.me).

To build each image, append the value of the definition after
`http://yuml.me/diagram/scruffy/class/edit/`.

* image/model/cart-component.png

[CartLine|cart;orderLine;purchasable]-1>[note: Purchasable{bg:cornsilk],
[OrderLine|order;cartLine;purchasable]-1>[note: Purchasable{bg:cornsilk],
[Cart|order;cartLines]1++-0..*>[CartLine],
[Order|cart;orderLines]<1-0..*>[OrderLine],
[Cart]<1-0..1>[Order],
[CartLine]<1-0..1>[OrderLine]

* image/model/cart-cartline.png

[CartLine|cart;orderLine;purchasable]-1>[note: Purchasable{bg:cornsilk],
[Cart|order;cartLines]1++-0..*>[CartLine]

* image/model/order-orderline.png

[OrderLine|order;cartLine;purchasable]-1>[note: Purchasable{bg:cornsilk],
[Order|cart;orderLines]<1-0..*>[OrderLine]