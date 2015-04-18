CouponBundle Reference
======================

```yaml
elcodi_coupon:
    
    mapping:
        coupon:
            # Coupon entity implementing CouponInterface
            class: Elcodi\Component\Coupon\Entity\Coupon
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCouponBundle/Resources/config/doctrine/Coupon.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
```