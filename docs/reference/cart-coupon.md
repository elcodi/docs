CartCouponBundle Reference
==========================

``` yaml
elcodi_cart_coupon:
    
    mapping:
        cart_coupon:
            # CartCoupon entity implementing CartCouponInterface
            class: Elcodi\Component\CartCoupon\Entity\CartCoupon
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCartCouponBundle/Resources/config/doctrine/CartCoupon.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
        order_coupon:
            # OrderCoupon entity implementing OrderCouponInterface
            class: Elcodi\Component\CartCoupon\Entity\OrderCoupon
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCartCouponBundle/Resources/config/doctrine/OrderCoupon.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
```
