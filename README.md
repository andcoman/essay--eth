Microservices aim to provide modularity and scalability, yet many systems built with them fall into monolithic approach by heavily relying on request-response.
While synchronously communication is more intuitive simpler and you DO NOT NEED strong consistency (in most of the casses you do not need it). Meaning to wait for
a response.

An asynchronous approach addresses these challenges by decoupling services. Using event-driven architecture, services publish events to a message bus
and other services consumes them independently. This ensures eventual consistency, where the system achieves a coherent state without synchronous dependencies.
Multi-step operations are required in most production casses, A very useful pattern for handlint distributed transactions are Sagas, when the small piece within the microservice context
is processed in pushes the result to the message bus, in case something goes wrong you can create a mechanism to rollback all your changes, across of all your microservices.

In the original synchronous workflow:

- A transaction request went through several services (e.g., fraud detection, authorization, settlement) sequentially.
  Each service called the next and waited for a response, resulting in high latency and tight coupling.
  With the asynchronous model:
- The transaction service publishes a "Transaction Initiated" event to a Kafka topic.
  Services like fraud detection, authorization, and settlement subscribe to this event and process it independently, publishing their results back to relevant topics.

Switching to an asynchronous:

- Services no longer block waiting for responses, significantly reducing overall latency.
  Each service operates independently, allowing for parallel processing.
  For multi-step operations, we used sagas to coordinate distributed transactions. Each step of the payment workflow had a compensating action for rollbacks:

- If a transaction (financial) failed during authorization, the saga triggered a refund action to reverse deductions.

