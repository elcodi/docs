BannerBundle Reference
======================

```yaml
elcodi_banner
    mapping:
        banner:
            # Banner entity implementing BannerInterface
            class: Elcodi\Component\Banner\Entity\Banner
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiBannerBundle/Resources/config/doctrine/Banner.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
        banner_zone:
            # BannerZone entity implementing BannerZoneInterface
            class: Elcodi\Component\Banner\Entity\BannerZone
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiBannerBundle/Resources/config/doctrine/BannerZone.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
```
