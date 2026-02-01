What’s more, as I explain in this chapter and the next, IPC
even intersects with transaction management. I favor an architecture consisting of
loosely coupled services that communicate with one another using asynchronous mes-
saging. Synchronous protocols such as REST are used mostly to communicate with
other applications.

the concept of self-contained services that handle synchronous requests with-
out communicating with other services in order to improve availability.

1. I start this section with a discussion of interaction styles, which are a technology-independent way of describing how clients and services
interact.
2. the importance of precisely defining APIs in a microservice
architecture, including the concept of API-first design.
3. the important topic of API evolution.
4. different options for message formats and how they can determine ease of API evolution

1.1:
It’s useful to first think about the style of interaction between a service and its clients
before selecting an IPC mechanism for a service’s API. Thinking first about the inter-
action style will help you focus on the requirements and avoid getting mired in the
details of a particular IPC technology:
  - the choice of interaction style impacts the availability of your application
  - it helps you select the appropriate integration testing strategy.


- client-service interaction styles can be categorized in two dimensions. 
  - The first dimension is whether the interaction is one-to-one or one-to-many:
    - One-to-one—Each client request is processed by exactly one service.
    - One-to-many—Each request is processed by multiple services.
  - The second dimension is whether the interaction is synchronous or asynchronous:
    - Synchronous—The client expects a timely response from the service and might even block while it waits.
    - Asynchronous—The client doesn’t block, and the response, if any, isn’t necessarily sent immediately.

|                     | One-to-One                     | One-to-Many                                      |
|---------------------|--------------------------------|--------------------------------------------------|
| **Synchronous**     | Request/response               | -                                                |
| **Asynchronous**    | Asynchronous request/response, One-way notifications  |  Publish/subscribe, Publish/async responses |



one-to-one interactions:
- Request/response—A service client makes a request to a service and waits for a
response. The client expects the response to arrive in a timely fashion. It might
event block while waiting. This is an interaction style that generally results in
services being tightly coupled.
- Asynchronous request/response—A service client sends a request to a service, which
replies asynchronously. The client doesn’t block while waiting, because the ser-
vice might not send the response for a long time.
-One-way notifications—A service client sends a request to a service, but no reply
is expected or sent.

A service can, for example, interact with another service using request/response style interaction with either REST or messaging. Even if two services are communicating using a message broker, the client service
might be blocked waiting for a response. It doesn’t necessarily mean they’re loosely coupled.


one-to-many interactions:
- Publish/subscribe—A client publishes a notification message, which is consumed
by zero or more interested services.
- Publish/async responses—A client publishes a request message and then waits for
a certain amount of time for responses from interested services.


---
1.2 Defining APIs in a microservice architecture: 
- A well-designed interface exposes useful functionality while hiding the implementation. It enables the implementation to change without impacting clients.
- a service’s API consists of operations, which clients can invoke, and events, which are
published by the service. An operation has a name, parameters, and a return type. An
event has a type and a set of fields and is published to a message channel.

- a service and its clients aren’t compiled together. If a new version of a service is deployed with an incompatible API, there’s no compilation error. Instead, there will be runtime failures.

- API-first approach to defining services:
  - First you write the interface definition. Then you review the interface definition with the client developers. Only after iterating on the API definition do you then implement the service. Doing this up-front design increases your chances of building a service that meets the needs of its clients.

- The nature of the API definition depends on which IPC mechanism you’re using. For example, if you’re using messaging, the API consists of the message channels, the message types, and the message formats. If you’re using HTTP, the API consists of the URLs, the HTTP verbs, and the request and response formats. Later in this chapter, I explain how to define APIs. A service’s API is rarely set in stone. It will likely evolve over time.

--- 
- 1.3 Evolving APIs
  - In a monolithic application, it’s relatively straightforward to change an API and update all the callers. If you’re using a statically typed language, the compiler helps by giving a list of compilation errors.
  - In a microservices-based application, changing a service’s API is a lot more diffi-
cult. A service’s clients are other services, which are often developed by other teams.
The clients may even be other applications outside of the organization. You usually
can’t force all clients to upgrade in lockstep with the service. Also, because modern
applications are usually never down for maintenance, you’ll typically perform a rolling
upgrade of your service, so both old and new versions of a service will be running
simultaneously.

- semantic versioning:
  - MAJOR.MINOR.PATCH
    - MAJOR—When you make an incompatible change to the API
    - MINOR—When you make backward-compatible enhancements to the API
    - PATCH—When you make a backward-compatible bug fix
  - If you’re implementing a REST API, you can, as mentioned below, use the major version as the first element of the URL path. Alternatively, if you’re implementing a service that uses messaging, you can include the version number in the messages that it publishes. The goal is to properly version APIs and to evolve them in a controlled
fashion.
  - MAKING MINOR, BACKWARD-COMPATIBLE CHANGES :Ideally, you should strive to only make backward-compatible changes. Backward-compatible changes are additive changes to an API:
    - Adding optional attributes to request
    - Adding attributes to a response
    - Adding new operations

  - Robustness principle: “Be conservative in what you do, be liberal in
what you accept from others.” Services should provide default values for missing
request attributes. Similarly, clients should ignore any extra response attributes. In
order for this to be painless, clients and services must use a request and response for-
mat that supports the Robustness principle.

  - If you’re using an HTTP-based IPC mechanism, such as REST, one approach is to embed the major version number in the
URL. For example, version 1 paths are prefixed with '/v1/…', and version 2 paths with '/v2/…' 
    - Another option is to use HTTP’s content negotiation mechanism and include the version number in the MIME type. (Accept header)

---
1.4 message format:
- The choice of message format can impact the efficiency of IPC, the usability of the API, and its evolvability.
- it’s essential to use a cross-language message format. Even if you’re writing your microservices in a single language today, it’s likely that you’ll use other languages in the future. You shouldn’t, for example, use Java serialization.

- There are two main categories of message formats: text and binary. Let’s look at
each one.
  - TEXT-BASED MESSAGE FORMATS
  - BINARY MESSAGE FORMATS
    - Popular formats include Protocol Buffers and Avro.
    - Both formats provide a typed IDL for defining the structure of your messages. A compiler then generates the code that serializes and deserializes the messages. You’re forced to take an API-first approach to service design! Moreover, if you write your client in a statically typed language, the compiler checks that it uses the API correctly.
One difference between these two binary formats is that Protocol Buffers uses tagged fields, whereas an Avro consumer needs to know the schema in order to interpret messages. As a result, handling API evolution is easier with Protocol Buffers than with Avro. This blog post(https://martin.kleppmann.com/2012/12/05/schema-evolution-in-avro-protocol-buffers-thrift.html)
