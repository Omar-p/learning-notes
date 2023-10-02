pluralsight

Spring Integration: Getting Started.
1

- how to connect ur internally compnents in ur spring app with
  external systems using asynchronous messaging. without needing
  of writing plumbing code [downloading file from FTP server:
  - u will need to establish connection.
  - authenticate
  - navigating files...
    ]sping-integration[
  - configure FTP inbound Channel Adapter that downloads file on
    regular schedule and put a message on a channel
    whenever the file is downloaded , then.
  - configure Service Activator that call our business logic
    when it recieves a message on the channel.
    ].
- separate integration code from business logic.
- basis of other spring projects [cloud stream
  for building highly scalably event-driven microservice]
- integrate different system have challenges such as :
  - 1- each system may produce different data format.
  - asynchronous adv. 
    - 1- . More responsive system because sender does not have to wait. 
    - 2- Messages buffering makes the system resilient to failure:
    after the receiver recover from failure, the receiver will find msgs in the Q 
    - 3- Receiver processes messages at its own rate.  
  - 2- a typical spring integration app will has a multiple MessageChannel,
    so when we inject it. we may need to use @Qualifier("beanName") to
    explicitly specify what we want.

- use classes from org.springframework.messaging over org.springframework.integration:\

  - historical they were in integration package, people find them so useful <br>  so they add them to spring core. for backward competability they don't remove <br>  it from ..integration

### dsl
- give us a set of builders to configure spring integration flo <br> IntegrationFlow initializ set of spring integration compnents and wire <br> them together. 
  - IntegrationFlow.from(startingPoint).handle([class and method want service activator to call])

## Channels
  ### subscribable channel:
  - implement event-driven consumer pattern, push msgs to rec, Non-buffering
    - Implementation:
      - Unicasting dispatcher[DirectChannel, ExectuorChannel] (pattern: p2p)
      - broadcasting dispatcher[PublishSubscribeChannel] (pattern (pub-sub))
      - DirectChannel: 
        - call one of its subscriber when the msg is sent on the same thread.
      - ExectuorChannel: 
        - p2p that delegates to an instance of TaskExecutor to perform msg dispatching, <br> shouldn't be used for transactions that span channel calls doesn't need to block the sender.

  ### Pollabl Channel: 
    - Polling Consumer create a separate thread and periodically poll the channel for msgs, Pull, Buffering. 
    - QueueChannel
    - default: use linked blocking queue with unbounded capacity. [u can configure type or capacity] <br>  if q is empty thee receiver block until the sender send msg or timeout.
  ### RendezvousChannel
    - ZeroCapacity Queue channel, Block until sender and receiver meet. 
      - Backed by SynchronizeQueue 
      - p2p, synchronize sender and receiver[check availability of something], best use for the request-reply pattern. 
      - PriorityQueue
         - Default oredering by "priority" header, can be overrided[by definig ur own Comparator] 
- pollable and buffered - stored message in PriorityBlockingQueue - Potential to starve message with low priority.
- DirectChannel 
  - p2p subscribable that enables a single thread to perform operations on both sides of the channel. 
  - when the pub invokes its send method, it forwards the msg in that same thread to its msg dispatcher, <br> which invokes the subscriber's handleMsg method. when sub completes, the the response is <br> returned to the pub. -> supporting transaction. 
  - Motivation: Transaction Integrity: 
    - support transactions that must span across the channel while still benefiting from the abstraction <br> and loose coupling that channel provides. 
    - sender block on the send() until receiver complete msg processing  
    - best choice for transactional calls that span across a channel, while retaining loose coupling.

3

- to send msg to mq we need to convert it to suitable format 
  - java serialization drawbacks:
    - 1-a serialized java obj is a binary blob of data which is not easily human readable.
    - 2-tightly coupled with a specific version of java class.
      - if u make change to the class, then u might get an incompatible class change error when u read = > an obj that was serialized with an older version.
  - json: need to use object-to-json-transformer [need input-channel, output-channel, ]
  - Message Broker : 
    - an intermediary computer program module that translates a message from the formal messaging  protocol of the sender to the formal messaging protocol of the receiver. 
    - decouple sender from the receiver.
    - ex: [to configure in spring]
      - [sender(input-channel, output-channer, transformer, adapter)]
        - adapter: a glue between spring integration msg channels and other components and systems. 
          - spring has many impl to it, allowing spring to connect easily with different systems.
  
---
# Errors in Asynchronous Applications: 
  - an error occurred in the receiver, what does receiver do with the msg? 
    - in request-reply pattern > standards exception handling. 
    - Direct channel - RendezvousChannel 
      - execute gateway in try-block. spring make it easy: u can handle error gracefully. 
      - in directChannel u don't have to define return type for gateway. because directChannel use 1 thread to <br>  handle sending/receiving u can just wrap gateway execution with try-catch even if it's void.

---

Message Design

- Command Message.
  - app1 invoke functionality provided by another app.
  -
- Request-Reply Message.
  facilitates a two-way conversation between apps
- Document Message.
  - transfer data to another app.
  - when sending a group of msg, we may add to headers:
    - SequenceNum, SequenceSize
- Event Message.
  allow app to pub event when an internal state change.
- the code-level impl differences between them are minimal ,
  the decision on when to use or the order is significant.
  Easiest way in spring integration to publish msg to a channel is by using
  @MessagingGateway and provide the default request message channel.

## @MessageEndpoint is to define that svc is able to serve/accept/receive a msg, has a @ServiceActivator on a method.

- Request-Reply Message [equiavlent to making a rpc ]

  - A Request-Reply Message is used to facilitate a two-way
    conversation via messaging.
  - sender send through channel , spring integration automatically
    a temp reply channel and adds it to the msg header.
    and create errorChannel if the svcActivator method throws an exception,
    then the service activator publishes the exception to the error channel.
    receiver reply through a reply channel.
  - advantages over normal rpc:
    - > Receiver can change over time without requiring
      > a change to the sender. - we send/receive from a broker. no need to know about the other system.

  I think : it's sysnchronized/blocking

---

- Event Messages:
  An app publishes events when its internal state has changed.
  other applications can integrate with that application
  by listening to its events.

. High scalable : a system can integrate with other systems asynchronously
. Loosely Coupled : A system need to know about the other systems with which it is integerating.

. cons: data is eventually consistent, take some times until all components have
the same representation of the data.

---

Heavyweight and lightweight event:
Heavyweight : contains a full payload with all the info about the event.
lightweight: contains the ID of the record that changed, as well as any other
info needed to make a callback[observer need to call the subject].

- Expiration Date header is a good way to tell
  consumers how long a message is good for.

---

RabbitMQ:

- producer send msg land inside exchange -based on routing pattern forward to a paradigm q [binding]
  =>
- Exchange Type -- -- -- Default pre-declared names:
  .Direct (empty string) and amq.direct
  .Fanout amq.fanout
  .Topic amq.topic
  .Header exchange amq.match(and amq.headers in RabbitMQ)
  .Default: nameless routing key = q name

---

docs
#Overview
Spring Integration’s primary goal is to provide a simple model for building
enterprise integration solutions while maintaining the separation of concerns
that is essential for producing maintainable, testable code.

dependency injection relieves the components of the responsibility of locating
or creating their dependencies.

aspect-oriented programming relieves business components of generic cross-cutting
concerns by modularizing them into reusable aspects.

supports message-driven architectures where inversion of control applies to
runtime concerns, such as when certain business logic should run and where the
response should be sent. It supports routing and transformation of messages so
that different transports and different data formats can be integrated without
impacting testability. In other words, the messaging and integration concerns
are handled by the framework.

Spring Integration goals: - enforcing separation of concerns between business logic and integration logic. - components should be loosely coupled. - Extension points should be abstract in nature (but within well-defined
boundaries) to promote reuse and portability.

Components:
messaging systems typically follow the similarly abstract “pipes-and-filters”
model. The “filters” represent any components capable of producing or consuming
messages, and the “pipes” transport the messages between filters so that the
components themselves remain loosely-coupled.

The underlying messaging infrastructure that supports the “pipes” should still
be encapsulated in a layer whose contracts are defined as interfaces. Likewise,
the “filters” themselves should be managed within [a layer that is logically above
the application’s service layer], interacting with those services through
interfaces in much the same way that a web tier would.

Message[Headers(unique id,timestamp,correlation id,ret addr)+Payload(app-specific content)]:
a generic wrapper for any java object combined with metadata used by the framework
while handling that object.
The payload can be of any type, and the headers hold commonly required information
such as ID, timestamp, correlation ID, and return address.
Headers contains required data for handling the message properly: - if a message’s content is ultimately going to be sent by an outbound
mail adapter, the various properties (to, from, cc, subject, and others)
may be configured as message header values by an upstream component. - Developers can also store any arbitrary key-value pairs in the headers.

Message Channel(“pipe” of a pipes-and-filters architecture): - The message channel therefore decouples the messaging components and also
provides a convenient point for interception and monitoring of messages. - A message channel may follow either 1. point-to-point[ex: Queue]
[no more than one consumer can receive each message sent to the channel.] - default to a blocking queue. - receiver[poller]:
trigger: fixedDelay[delay + processingTime], fixedRate [count processing from waiting time],
cronJob defines when channel is polled. @ServiceActivator(inputChannel, poller = @Poller())
allows consumer to throttle incoming messages. 2. publish-subscribe semantics[ex: Event notifications]
[attempt to broadcast each message to all active subscribers on the channel.] - if subscribers come and go and want access to historical msgs,
integrate with broker and configure persistence there; - another important consideration: Should the channel buffer messages? - pollable channels are capable of buffering Messages within a queue.
The advantage of buffering is that it allows for throttling the inbound
messages and thereby prevents overloading a consumer.
adds some complexity, since a consumer can only receive the messages from
such a channel if a poller is configured.  
 On the other hand, a consumer connected to a subscribable channel is simply
message-driven (Message Channel Implementations: https://docs.spring.io/spring-integration/reference/html/channel.html#channel-implementations)

Message Endpoint:
you should not have to implement consumers and producers directly, and you should not
even have to build messages and invoke send or receive operations on a message channel.
by providing declarative configuration, you can “connect” your domain-specific code to
the messaging infrastructure provided by Spring Integration.

- The components responsible for these connections are message endpoints.

  - part of code where the sender/receiver connect to the messaging system.

- Message Transformation:
  - responsible for converting a message’s content or structure and returning the modified message.
- Message filter:

  - requires a boolean test method that may check for a particular payload content type, a property value,
    the presence of a header, or other conditions.

  - often used in conjunction with a publish-subscribe channel, where multiple consumers may receive the same
    message and use the criteria of the filter to narrow down the set of messages to be processed.

- Message Routing.
  - when receiver is not fixed or u chose from many, the Message-Router component
    will make the decision based on criteria we define(Message-Filter)[some-header..], or decide
    to drop it.
- Splitter
  - responsibility is to accept a message from its input channel, split that message into multiple
    messages, and send each of those to its output channel. This is typically used for dividing a
    “composite” payload object into a group of messages containing the subdivided payloads.

you should be able to focus on your specific domain model with an implementation based on
plain objects. Then, by providing declarative configuration, you can “connect” your
domain-specific code to the messaging infrastructure provided by Spring Integration.
The components responsible for these connections are message endpoints.
This does not mean that you should necessarily connect your existing application code directly.
Any real-world enterprise integration solution requires some amount of code focused upon
integration concerns such as routing and transformation. The important thing is to achieve
separation of concerns between the integration logic and the business logic. In other words,
as with the Model-View-Controller (MVC) paradigm for web applications, the goal should be to
provide a thin but dedicated layer that translates inbound requests into service layer invocations
and then translates service layer return values into outbound replies

---Josh long--

IntegrationFlow. from([]) u can originate from an inbounded adapter or GW,
which is something that take real event from real world and turn it into a spring boot message,
or you can originate it from a channel[pipe between components(an implicit connection)].

in spring integration flow when u have a component that return null,
that also terminate the pipline/flow,.

conceptually every compnent has another channel that can connect to another things.

----events---

# Events:

- Coupling refers to a degree of knowledge that one object has about the other object that it interacts with.
  - Coupling refers to a degree of knowledge that one object has about the other object that it interacts with.
- Event driven are architecture also follows an open-closed principle. Which means open for extension, but closed for modification.
- Event system allows invoking logic on multiple components at the same time.

# components

- Events
  - An ApplicationEvent is a simple POJO class that holds data and is exchanged between publisher and listener.
  - we don't need to extend ApplicationEvent class.
- Publisher
- Listeners.
  - limitation of ApplicationListener
    - ApplicationListener interface only supports void return type.
    - It can only be used for objects that extend ApplicationEvent class.
    - Listener can only process one event type.
  - there are different flavors of implementing listeners to an event. It can be implemented within notations or by implementing Application Listener.
    <br>Listeners, The neat thing is that Spring does all the heavy lifting for us and registered all listeners.
    - <img src="images/event-listener.png" alt="event-listener" style="width:450px;"/> <br />
  - Async listener
    - add @Async annotation to the listener method. @EnableAsync annotation to the configuration class.
    - limitations:
      - <img src="images/asyn-listener-limitations.png" alt="asyn-listener-limitations" style="width:450px;"/> <br />
  - Unlike ApplicationListener interface, which only supports void return type, annotated methods may have a nonvoid return type. When they do, the result of the method in vocation is send as a new event. If the return type is either an array or a collection, each element is send as a new individual event. It is also possible to define the order in which listeners for the same event are to be invoked. To do so, we can use SpringCommon @order annotation, alongside EventListener annotation
    - <img src="images/publish-ret-obj-as-events.png" alt="publish-ret-obj-as-events" style="width:450px;"/> <br />

# processing

    - Synchronous By defaults. meaning the publisher thread blocks until all listeners have finished processing the event.
    - Asynchronous, meaning that it went as published in a new thread and release execution of publisher independently from the listener.
    - Transection Bound Events, Spring allows us to bind an Event Listener to phase of the current transaction. This allows events to be used with more flexibility when the outcome of the current transaction matters to the listener.

- <img src="images/transaction-bound-event.png" alt="transaction-bound-event" style="width:450px;"/> <br />
- Filtering Events, Spring Events gives us an option to find tune conditions under which circumstances our Event Listener will be triggered.
  - <img src="images/events-filtering.png" alt="events-filtering" style="width:450px;"/> <br />
- if we have a use case where we want our listener to be triggered before Application Context is created(for predefined events), we need to register those listeners manually. SpringApplication#addListeners() method allows us to do that.
- imagine adding promotion for a new customer or updating some external systems. We not only need to provide an email service, but also all other functionalities that are coming up and centered around customer registration. We might argue, why should the customer registration process need to know about all those first functionalities that are centered around it? This creates more cyclic dependencies, and violates the single responsibility principle. Testing becomes much harder as we might need to mock all different dependencies.

---

rabbitmq:

- LPC vs RPC
- RPC is not LPC with a longer wire
  - there many many things can go wrong with RPC, that couldn't go wrong with LPC.
  - RPC is a distributed system, and distributed system are hard.
  - remember fallacies of distributed computing.
  - if client make a request with a side effect and didn't receive the response because of any server error or network issues. it's not safe if the client replay that request again.
    - in LPC, There's no scenario where the function we call would never return back to us. {return the result, throw exception, or return a failure}
- Messaging

  - client send message to a queue, and we can gurentee that we will know the message will be delivered or not.
  - server asynchronosly process the message and send the result to another queue.
    - if the server take a message and blow up and hasn't yet acknowledged , the message will be requeued and another server will pick it up.
  - we have different type of processing {fire and forget, request and reply, publish and subscribe}
  - typically messaging is used for one way communication, but we can use it for RPC(two way communication).
  - retry on consumer side, if the consumer fail to process the message, it will be requeued and another consumer will pick it up. be aware of poison message.
    - poison message is a message that cause the consumer to fail, and it will be requeued again and again. rabbitmq has a feature called dead letter exchange, which allow us to send the message to another queue after a number of retries.
  - Messaging dealing with disconnected systems, and it's a good fit for microservices.
  - no way for producer to know if the message has been processed or not.
    - we can use correlation id to correlate the request and the response.

- JMS a common API
  - no common protocol, if u choose two different JMS provider, there is no guarantee that these things will be able to talk to each other.
  - standard impl: ActiveMQ, IBM MQ, RabbitMQ, WebSphere MQ, JBoss.
- other option : AMQP
  - AMQP is a protocol, and RabbitMQ is an implementation of that protocol.
  - any broker that implements a AMQP implements the same protocol, and we can use any client to talk to any broker.
