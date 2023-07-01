# Node-interview-questions

## Table of Contents

1. [What are Event Emitters](#what-are-event-emitters)
2. [How to pass data with event emitters in Node.js](#how-to-pass-data-with-event-emitters-in-nodejs)
3. [How to handle errors with event emitters in Node.js](#how-to-handle-errors-with-event-emitters-in-nodejs)
4. [How to handle multiple event listeners in Node.js](#how-to-handle-multiple-event-listeners-in-nodejs)
5. [How to remove event listeners in Node.js](#how-to-remove-event-listeners-in-nodejs)
6. [What is the difference between `removeListener` and `removeAllListeners` in event emitters](#what-is-the-difference-between-removelistener-and-removealllisteners-in-event-emitters)
7. [What is the difference between `on` and `once` methods in event emitters](#what-is-the-difference-between-on-and-once-methods-in-event-emitters)
8. [How event emitters are different from Pub/Sub ques like SQS ( Amazon Simple Queue Service )](#how-event-emitters-are-different-from-pubsub-ques-like-sqs--amazon-simple-queue-service-)

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

## How to pass data with event emitters in Node.js?

You can pass data along with event emitters by passing additional arguments to the `emit()` method. Here's an example:

```node
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

myEmitter.on('myEvent', (data) => {
  console.log('Received data:', data);
});

myEmitter.emit('myEvent', 'Hello, World!');
```

## How to handle errors with event emitters in Node.js?

To handle the errors, there is a standard convention that an `error` event to be used followed by an Error object as argument. You can listen for this event and handle any errors that occur. Here's an example:

```node
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

myEmitter.on('error', (error) => {
  console.error('An error occurred:', error);
});

myEmitter.emit('error', new Error('Something went wrong!'));
```

## How to handle multiple event listeners in Node.js?

You can attach multiple event listeners to an event using the `on()` or `addListener()` methods. Each listener will be invoked when the event is emitted. Here's an example:

```node
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

const listener1 = () => {
  console.log('Listener 1');
};

const listener2 = () => {
  console.log('Listener 2');
};

const listener3 = () => {
  console.log('Listener 3');
};

myEmitter.on('myEvent', listener1);
myEmitter.on('myEvent', listener2);
myEmitter.addListener('myEvent', listener3);

myEmitter.emit('myEvent');
```

## How to remove event listeners in Node.js?

The `removeListener()` method can be used to remove a specific event listener. Here's an example:

```node
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

const listener = () => {
  console.log('Event emitted!');
};

myEmitter.on('myEvent', listener);

myEmitter.emit('myEvent');  // Event emitted!

myEmitter.removeListener('myEvent', listener);

myEmitter.emit('myEvent');  // No output
```

## What is the difference between `removeListener` and `removeAllListeners` in event emitters?

The `removeListener(eventName, listener)` method and the `removeAllListeners(eventName)` method in event emitters have different purposes:

1. `removeListener(eventName, listener)`:
   - This method is used to remove a specific event listener attached to an event.
   - It takes two arguments: the `eventName` (string) and the `listener` (function) that was previously attached.
   - When called, it removes the specified `listener` function from the event's listener array.
   - If there are multiple listeners attached to the event, only the specific listener provided will be removed.
   - If the same listener function was attached multiple times, only one occurrence will be removed.
   - If no listener function is found matching the provided listener, nothing happens.

2. `removeAllListeners(eventName)`:
   - This method is used to remove all event listeners attached to a specific event.
   - It takes one argument: the `eventName` (string) from which all listeners should be removed.
   - When called, it removes all listener functions associated with the specified event, effectively clearing all event listeners for that event.
   - If no event name is provided, it removes all listeners for all events associated with the event emitter instance.
   - After calling `removeAllListeners()`, the event emitter will no longer emit any events associated with the provided event name.

In summary, `removeListener()` is used to remove a specific listener from an event, while `removeAllListeners()` is used to remove all listeners associated with a specific event or all events.

## What is the difference between `on` and `once` methods in event emitters?

The `on(eventName, listener)` and `once(eventName, listener)` methods in event emitters have differences in how they handle event listeners:

- `on(eventName, listener)`:
  - The `on()` method is used to attach a persistent event listener to an event.
  - It takes two arguments: the `eventName` (string) and the `listener` (function) to be executed when the event is emitted.
  - The listener function will be invoked every time the event is emitted.
  - Even if the event is emitted multiple times, the listener will be called for each occurrence.

Example:

```node
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

const eventListener = () => {
  console.log('Event emitted!');
};

myEmitter.on('myEvent', eventListener);

myEmitter.emit('myEvent');  // Event emitted!
myEmitter.emit('myEvent');  // Event emitted!
```

- `once(eventName, listener)`:
  - The `once()` method is used to attach a one-time event listener to an event.
  - It takes two arguments: the `eventName` (string) and the `listener` (function) to be executed when the event is emitted.
  - The listener function will be invoked only for the first occurrence of the event.
  - After the listener is invoked, it is automatically removed from the event emitter, so it won't be called again for subsequent occurrences of the event.

Example:

```node
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

const eventListener = () => {
  console.log('Event emitted!');
};

myEmitter.once('myEvent', eventListener);

myEmitter.emit('myEvent');  // Event emitted!
myEmitter.emit('myEvent');  // No output
```

To summarize, `on()` attaches a persistent event listener that is called every time the event is emitted, while `once()` attaches a one-time event listener that is automatically removed after the first occurrence of the event.

## How event emitters are different from Pub/Sub ques like SQS ( Amazon Simple Queue Service )?

Event emitters and publish/subscribe (pub/sub) queues, such as Amazon Simple Queue Service (SQS), have some similarities but serve different purposes and have distinct characteristics. Here are the key differences between event emitters and pub/sub queues:

1. **Communication Model**:- Event Emitter follow a rather direct communication model, where there will be an emitter which will emit some message and that message will be received by some subscribed listeners. While, In Pub/Sub queues publishers publish messages to a queue or topic, and subscribers receives message from these queues or topics.
2. **Message Persistance**:- Pub/Sub queues, like SQS persists message in the queue until they are not consumed by the subscribers or deleted explicitly. Queues allows subscriber to consume messages at their own pace. In contrast, event emitters do not persists messages by default, if there are no active listeners at the time when message will be published, they message will get lost.
3. **Message Ordering**:- Pub/sub queues, such as SQS, often guarantee message ordering within a single queue while event emitter do not provide inherent ordering guarantees.
4. **Scalability and Decoupling**:- : Pub/sub queues like SQS are designed to handle high-throughput scenarios and provide decoupling between publishers and subscribers. Publishers and subscribers can operate independently and at different rates. Event emitters, although they can be used for asynchronous communication, are often more tightly coupled since they directly trigger listeners within the same process.
5. **Message Delivery**:- Pub/sub queues, such as SQS, typically support reliable delivery guarantees. Messages are stored durably and can be delivered reliably to subscribers even in the presence of failures. Event emitters within a single process do not provide built-in mechanisms for reliable message delivery, as they rely on direct function calls and do not persist messages.
