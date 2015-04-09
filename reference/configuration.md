Configuration Reference
=======================

``` yaml
elcodi_configuration:
    mapping:
        configuration:
            # Configuration entity implementing ConfigurationInterface
            class: Elcodi\Component\Configuration\Entity\Configuration
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiConfigurationBundle/Resources/config/doctrine/Configuration.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
    # Set of configuration elements. Each one can be defined as parameter-based
    # configuration item, or not
    elements:
        my_configuration_item:
            # Configuration item key
            key: // required, for example "item"
            # Configuration item name
            name: // required, for example "Item"
            # Configuration item namespace.
            namespace: ''
            # You can define the type of this value. Chose between boolean,
            # string, text or array
            type: // required, for example "string"
            # You can reference a parameter by setting its name
            reference: ~
            # Default value for when is not defined
            default_value: ~
            # This configuration element can be empty
            can_be_empty: true
            # When cannot be empty and not value is found, you can define what
            # message you want to show
            empty_message: ''
            # Read-only. This value cannot be modified or altered in database
            read_only: false
```
