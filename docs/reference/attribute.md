AttributeBundle Reference
=========================

``` yaml
elcodi_attribute:
    
    mapping:
        attribute:
            # Attribute entity implementing AttributeInterface
            class: Elcodi\Component\Attribute\Entity\Attribute
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiAttributeBundle/Resources/config/doctrine/Attribute.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
        value:
            # Value entity implementing ValueInterface
            class: Elcodi\Component\Attribute\Entity\Value
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiAttributeBundle/Resources/config/doctrine/Value.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
```
