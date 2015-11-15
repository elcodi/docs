# How to implement an Event Listener

One of the good reasons to work with Symfony is because has a great and big
extension point. Symfony uses it's own Event Dispatcher component.

# Scenario

Our project needs to grow with new features related to existing ones. Because of
Elcodi is always installed as a vendor, we should not be able to modify it at 
all. For example, each time an order is created, we should be able to send the
administrator of the website a new email.

We need to know how Event Listeners must be created in order to use them 
properly, using Elcodi best practices and naming conventions.

# Solution

When we talk about Event Listeners in Symfony, we should think about a service 
with an extra layer of configuration. For example, following the example exposed
above, let's create a new service that sends that email we need.

Of course, this is not only a service but an EventListener, so the chosen method
must accept only one parameter. Depending of the event you want to hook your 
method, you will receive one Event type or other.

> You should know something about the Event Dispatcher from Symfony. We strongly
> recommend you to read something about it before continue reading this 
> documentation.

Each type of event is associated with a different type of event instance, so you
should 

``` php
use Elcodi\Component\Cart\Entity\Interfaces\OrderInterface;
use Elcodi\Component\Cart\Event\OrderOnCreatedEvent;

/**
 * This class is intended to send an Email when any order is created
 */
class OrderEmailSenderEventListener
{
    /**
     * @var string
     *
     * Address where the email has to be sent
     */
    private $email;

    /**
     * Send an email to the administrator of the website, once an Order has been
     * successfully created.
     *
     * @param OrderOnCreatedEvent $event Order on Created event
     *
     * @return $this Self object
     */
    public function sendEmail(OrderOnCreatedEvent $event)
    {
        $order = $event->getOrder();
        // Send that email using $this->email!
     
        return $this;
    }
}
```

### Isolating your business logic

Our last implementation has one issue if you consider important your business
logic.

Imagine that you need to send this email from another service. Of course, this
service should know nothing about any kind of event, so, using last 
implementation, something is clearly wrong.

Let's see how we can fix improve this by doing just some small changes!

``` php
use Elcodi\Component\Cart\Entity\Interfaces\OrderInterface;
use Elcodi\Component\Cart\Event\OrderOnCreatedEvent;

/**
 * This class is intended to send an Email when any order is created
 */
class OrderEmailSenderEventListener
{
    /**
     * @var string
     *
     * Address where the email has to be sent
     */
    private $email;

    /**
     * Send an email to the administrator of the website, once an Order has been
     * successfully created.
     *
     * @param OrderOnCreatedEvent $event Order on Created event
     *
     * @return $this Self object
     */
    public function sendEmail(OrderOnCreatedEvent $event)
    {
        $order = $event->getOrder();
        $this->sendEmailByOrderAndEmail(
            $order,
            $this->email
        );
     
        return $this;
    }
     
     /**
      * Send an specific email given an order instance and the address where the
      * email must be sent
      *
      * @param OrderInterface $order Order
      * @param string         $email Email
      *
      * @return $this Self object
      */
    public function sendEmailByOrderAndEmail(OrderInterface $order, $email)
    {
        // Send that email using $this->email!
      
        return $this;
    }
}
```

With this implementation you can still be listening to the `order.oncreated`
event, and your business logic can be accessed from any point of entry.

### Using Dependency Injection Tags

Once the class is created and tested, we should add this extra configuration
layer in our Dependency Injection definition. And we will do such thing by using
tags.

> Once again, this is not Elcodi related, but Symfony. You can check a little 
> bit more information by checking the Symfony Docs

Let's see an example of how this configuration could be.

``` yaml
services:
    
    elcodi.event_listener.order_email_sender:
        class: My\Bundle\EventListener\OrderEmailSenderEventListener
        arguments:
            - %administrator_email%
        tags:
            - { name: kernel.event_listener, event: order.oncreated, method: sendEmail }
```
