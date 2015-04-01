CartBundle Reference
====================

``` yaml
elcodi_cart
    mapping:
        cart:
            # Cart entity implementing CartInterface
            class: Elcodi\Component\Cart\Entity\Cart
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCartBundle/Resources/config/doctrine/Cart.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
        cart_line:
            # CartLine entity implementing CartLineInterface
            class: Elcodi\Component\Cart\Entity\CartLine
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCartBundle/Resources/config/doctrine/CartLine.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
        order:
            # Order entity implementing OrderInterface
            class: Elcodi\Component\Cart\Entity\Order
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCartBundle/Resources/config/doctrine/Order.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
        order_line:
            # OrderLine entity implementing OrderLineInterface
            class: Elcodi\Component\Cart\Entity\OrderLine
            # Doctrine mapping file for this entity
            mapping_file: '@ElcodiCartBundle/Resources/config/doctrine/OrderLine.orm.yml'
            # Doctrine manager name
            manager: default
            # Is this entity enabled?
            enabled: true
    
    cart:
        # Your cart is saved in session
        save_in_session: true
        # If your cart is saved in session, what's the field name?
        Session_field_name: cart_id
    
    # You can define a set of states inside the payment machine
    # This machine is used as the payment workflow guide
    payment_states_machine:
        # Name for this machine in order to be cached
        identifier: order_payment_states_machine
        # Which state will be the first one? Take in account that such state 
        # will be added automatically once the order is created
        point_of_entry: unpaid
        # Set of states conforming this machine
        states:
            - ["unpaid", "pay", "paid"]
            - ["paid", "refund", "refunded"]
            
    # You can define a set of states inside the shipping machine
    # This machine is used as the shipping workflow guide
    shipping_states_machine:
        # Name for this machine in order to be cached
        identifier: order_shipping_states_machine
        # Which state will be the first one? Take in account that such state 
        # will be added automatically once the order is created
        point_of_entry: not shipped
        # Set of states conforming this machine
        states:
            - ["not shipped", "ship", "shipped"]
```
