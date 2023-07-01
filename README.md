# Node-interview-questions

## Table of Contents

1. [What are Event Emitters](#what-are-event-emitters)
2. [How event emitters are different from Pub/Sub ques like SQS ( Amazon Simple Queue Service )](#how-event-emitters-are-different-from-pubsub-ques-like-sqs--amazon-simple-queue-service-)

## What are Event Emitters?

In Node.js, an event emitter is a core module that allows you to implement the publisher-subscriber pattern. It provides an implementation of the observer pattern, where objects (known as event emitters) can emit named events, and other objects (known as event listeners or subscribers) can listen for those events and react accordingly.

```node
const EventEmitter = require('events');

// Create an instance of EventEmitter
const myEmitter = new EventEmitter();

// Register an event listener
myEmitter.on('myEvent', (arg1, arg2) => {
  console.log('Event occurred with arguments:', arg1, arg2);
});

// Emit an event
myEmitter.emit('myEvent', 'Hello', 'World');
```

## How event emitters are different from Pub/Sub ques like SQS ( Amazon Simple Queue Service )?

Event emitters and publish/subscribe (pub/sub) queues, such as Amazon Simple Queue Service (SQS), have some similarities but serve different purposes and have distinct characteristics. Here are the key differences between event emitters and pub/sub queues:

1. **Communication Model**:- Event Emitter follow a rather direct communication model, where there will be an emitter which will emit some message and that message will be received by some subscribed listeners. While, In Pub/Sub queues publishers publish messages to a queue or topic, and subscribers receives message from these queues or topics.
2. **Message Persistance**:- Pub/Sub queues, like SQS persists message in the queue until they are not consumed by the subscribers or deleted explicitly. Queues allows subscriber to consume messages at their own pace. In contrast, event emitters do not persists messages by default, if there are no active listeners at the time when message will be published, they message will get lost.
3. **Message Ordering**:- Pub/sub queues, such as SQS, often guarantee message ordering within a single queue while event emitter do not provide inherent ordering guarantees.
4. **Scalability and Decoupling**:- : Pub/sub queues like SQS are designed to handle high-throughput scenarios and provide decoupling between publishers and subscribers. Publishers and subscribers can operate independently and at different rates. Event emitters, although they can be used for asynchronous communication, are often more tightly coupled since they directly trigger listeners within the same process.
5. **Message Delivery**:- Pub/sub queues, such as SQS, typically support reliable delivery guarantees. Messages are stored durably and can be delivered reliably to subscribers even in the presence of failures. Event emitters within a single process do not provide built-in mechanisms for reliable message delivery, as they rely on direct function calls and do not persist messages.
