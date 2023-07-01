# Node-interview-questions

## Table of Contents

1. **Event-Emitters**
   1. [What are Event Emitters](#what-are-event-emitters)
   2. [How to pass data with event emitters in Node.js](#how-to-pass-data-with-event-emitters-in-nodejs)
   3. [How to handle errors with event emitters in Node.js](#how-to-handle-errors-with-event-emitters-in-nodejs)
   4. [What is the role of error events in EventEmitter](#what-is-the-role-of-error-events-in-eventemitter)
   5. [How to handle multiple event listeners in Node.js](#how-to-handle-multiple-event-listeners-in-nodejs)
   6. [How to remove event listeners in Node.js](#how-to-remove-event-listeners-in-nodejs)
   7. [What is the difference between `removeListener` and `removeAllListeners` in event emitters](#what-is-the-difference-between-removelistener-and-removealllisteners-in-event-emitters)
   8. [What is the difference between `on` and `once` methods in event emitters](#what-is-the-difference-between-on-and-once-methods-in-event-emitters)
   9. [How can you implement custom event emitters in Node.js](#how-can-you-implement-custom-event-emitters-in-nodejs)
   10. [What are the core features of EventEmitter](#what-are-the-core-features-of-eventemitter)
   11. [Explain the concept of event propagation in event emitters](#explain-the-concept-of-event-propagation-in-event-emitters)
   12. [An example of using EventEmitter in a real-world scenario](#an-example-of-using-eventemitter-in-a-real-world-scenario)
   13. [What kind of applications can be created using event system](#what-kind-of-applications-can-be-created-using-event-system)
   14. [What are memory leaks in event emitters and how to prevent them](#what-are-memory-leaks-in-event-emitters-and-how-to-prevent-them)
   15. [Explain the difference between EventEmitter and streams in Node.js](#explain-the-difference-between-eventemitter-and-streams-in-nodejs)
   16. [What are the limitations or performance considerations when using EventEmitter](#what-are-the-limitations-or-performance-considerations-when-using-eventemitter)
   17. [What is event batching](#what-is-event-batching)
   18. [What is the maximum number of listeners that can be attached on a single event emitter instance](#what-is-the-maximum-number-of-listeners-that-can-be-attached-on-a-single-event-emitter-instance)
   19. [How event emitters are different from Pub/Sub ques like SQS ( Amazon Simple Queue Service )](#how-event-emitters-are-different-from-pubsub-ques-like-sqs--amazon-simple-queue-service-)
2. **Streams**
   1. [What are streams in Node.js](#what-are-streams-in-nodejs)
   2. [What is the purpose of using streams in node](#what-is-the-purpose-of-using-streams-in-node)

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

## What is the role of error events in EventEmitter?

In EventEmitter, error events play a crucial role in handling and propagating errors that occur within event listeners. The role of error events can be summarized as follows:

1. **Error Propagation:** When an error occurs inside an event listener function, and that error is not caught within the function itself, EventEmitter will emit an `'error'` event. This allows the error to be propagated to the parent emitter or any other error event listeners registered for that emitter.

2. **Default Error Handling:** If there are no listeners registered specifically for the `'error'` event on an EventEmitter instance, Node.js will treat it as an unhandled exception and display the error message and stack trace on the console. This default behavior helps identify and debug errors that are not handled explicitly.

3. **Error Listener Registration:** By registering an event listener for the `'error'` event on an EventEmitter instance, you can define custom error handling logic. This listener will be invoked whenever an error event is emitted, allowing you to handle the error gracefully, log it, or perform any necessary cleanup operations.

4. P**reventing Uncaught Exceptions:** By having an error listener for the `'error'` event, you can prevent unhandled exceptions from crashing the application. By handling the error within the error event listener, you can gracefully recover or terminate the application in a controlled manner.

By handling error events appropriately, you can effectively manage and respond to errors that occur within event listeners and prevent unhandled exceptions from crashing your application.

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

## How can you implement custom event emitters in Node.js?

In Node.js, you can implement custom event emitters by creating a class that extends the built-in EventEmitter class. Here are the steps to implement a custom event emitter:

```node
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {
  performTask() {
    console.log('Performing a task...');
    
    // Emit an event when the task is complete
    this.emit('taskComplete');
  }
}

// Create an instance of the custom emitter
const myEmitter = new MyEmitter();

// Register an event listener
myEmitter.on('taskComplete', () => {
  console.log('Task completed!');
});

// Perform the task
myEmitter.performTask();
```

In this example, the custom `MyEmitter` class extends the `EventEmitter` class. It adds a `performTask` method that emits a `'taskComplete'` event. An event listener is registered using the on method to execute when the `'taskComplete'` event is emitted.

When you run this code, it will output:

```arduino
Performing a task...
Task completed!
```

## What are the core features of EventEmitter?

The core features of EventEmitter in Node.js are as follows:

1. **Event Registration:** EventEmitter allows you to register event listeners for specific events using the `on` or `addListener` method.

2. **Event Emission:** You can emit events using the `emit` method. When an event is emitted, all registered listeners for that event are invoked synchronously.

3. **Multiple Listeners:** EventEmitter supports multiple listeners for the same event. You can register multiple listeners for an event, and they will all be executed when the event is emitted.

4. **Event Removal:** EventEmitter provides methods to remove event listeners. The `off` or `removeListener` method allows you to remove a specific listener for an event, while the `removeAllListeners` method removes all listeners for a specific event or all events.

5. **Error Handling:** EventEmitter includes built-in error handling. If an error event is emitted and no listener is registered for that event, Node.js will throw an error and display the stack trace.

6. **Inheritance:** EventEmitter can be extended to create custom event emitters. This allows you to define your own events and behavior specific to your application or module.

7. **Asynchronous Event Handling:** EventEmitter can handle both synchronous and asynchronous event listeners. Asynchronous listeners can be registered using the `once` method, which automatically removes the listener after it has been invoked.

8. **Memory Management:** EventEmitter manages memory efficiently by using weak references for event listeners. This means that if there are no other references to a listener, it will be garbage collected.

## Explain the concept of event propagation in event emitters

In the context of EventEmitter in Node.js, the concept of event propagation refers to the process of propagating events from an emitting object to its parent or containing objects. However, it's important to note that EventEmitter itself does not have built-in support for event propagation like some other frameworks or libraries.

By default, EventEmitter operates within a single object or module, where events are emitted and listened to within that specific context. When an event is emitted, all the registered listeners for that event are invoked in the order they were added. The event propagation is limited to this scope.

We can create a custom event emitter that allows event propagation by forwarding events from a child object to its parent object.

```node
const EventEmitter = require('events');

class ParentEmitter extends EventEmitter {
  constructor() {
    super();
    this.child = new ChildEmitter();
    
    // Forward the child event to the parent
    this.child.on('childEvent', (data) => {
      this.emit('parentEvent', data);
    });
  }
}

class ChildEmitter extends EventEmitter {
  doSomething() {
    // Emitting an event in the child object
    this.emit('childEvent', 'Hello from child!');
  }
}

// Create an instance of the parent emitter
const parentEmitter = new ParentEmitter();

// Register an event listener in the parent
parentEmitter.on('parentEvent', (data) => {
  console.log('Parent event received:', data);
});

// Perform an action in the child object, which triggers event forwarding to the parent
parentEmitter.child.doSomething();
```

```arduino
Parent event received: Hello from child!
```

## An example of using EventEmitter in a real-world scenario?

Imagine you're building a chat application where users can send messages to each other. Whenever a new message arrives, you want to notify all connected clients in real-time.

First, you would require the `events` module in Node.js, which provides the EventEmitter class:

```javascript
const EventEmitter = require('events');
```

Next, you can create an instance of the EventEmitter class:

```javascript
const chatEmitter = new EventEmitter();
```

Now, let's say you have a WebSocket server that handles incoming chat messages. Whenever a new message arrives, you can emit an event using the `chatEmitter` instance:

```javascript
// WebSocket server handling incoming chat messages
websocketServer.on('message', (message) => {
  // Process the message and obtain the relevant data (e.g., sender, content)
  
  // Emit an event with the message data
  chatEmitter.emit('newMessage', { sender, content });
});
```

In another part of your application, you might have code responsible for sending these messages to connected clients:

```javascript
// Code that sends messages to connected clients
chatEmitter.on('newMessage', (message) => {
  // Send the message to all connected clients in real-time
  websocketServer.send(message);
});
```

## What kind of applications can be created using event system?

Event-based systems are incredibly versatile and can be used to build a wide range of applications. Here are a few examples:

1. **Real-time applications:** Event-driven architectures are particularly well-suited for building real-time applications, such as chat applications, collaborative tools, and multiplayer games. The system can emit events when relevant actions occur, and connected clients can receive those events in real-time, enabling instant updates and synchronization.

2. **Microservices and distributed systems:** Event-driven architectures are often employed in microservices and distributed systems to enable loose coupling and scalability. Services can communicate with each other by emitting events, allowing for asynchronous and decoupled communication between different components.

3. **IoT (Internet of Things) applications:** Event-driven systems are commonly used in IoT applications, where sensors and devices generate events that trigger actions or notifications. For example, a smart home system could emit events when a door is opened, a temperature threshold is exceeded, or a motion is detected.

4. **Workflow automation:** Event-based systems can be used to automate workflows and orchestrate the execution of tasks. Each step in the workflow can emit events to signal the completion or status change, allowing subsequent steps to react accordingly. This is often used in business process automation, job scheduling, and task coordination systems.

5. **Logging and monitoring:** Event-driven architectures can be valuable in logging and monitoring applications. Events can be emitted for various system events, errors, or performance metrics. These events can then be consumed by monitoring tools or logging services, providing real-time insights, alerts, or generating reports.

6. **Event sourcing and CQRS:** Event-driven architectures are the foundation of event sourcing and Command Query Responsibility Segregation (CQRS) patterns. Event sourcing captures every state change as an event, which can be used to rebuild the state of the system at any given point in time. CQRS separates the read and write operations, using events to update the read models asynchronously.

## What are memory leaks in event emitters and how to prevent them?

- **Unremoved Event Listeners:**

```node
const EventEmitter = require('events');

const emitter = new EventEmitter();

function eventListener() {
  console.log('Event occurred!');
}

emitter.on('event', eventListener);

// Oops! Forgot to remove the event listener
// emitter.removeListener('event', eventListener);

// The 'eventListener' function will still be held in memory
// even if it's no longer needed, causing a memory leak
```

To prevent a memory leak in this example, make sure to call `emitter.removeListener('event', eventListener)` when the event listener is no longer required.

- **Global EventEmitter Instances:**

```node
const EventEmitter = require('events');

const globalEmitter = new EventEmitter();

function eventListener() {
  console.log('Event occurred!');
}

globalEmitter.on('event', eventListener);

// The globalEmitter instance may be kept alive indefinitely,
// preventing associated objects from being garbage collected
```

To prevent a memory leak in this example, avoid using global EventEmitter instances and instead manage the scope and lifetime of the EventEmitter appropriately.

- **Avoiding Multiple Registrations:**

```node
const EventEmitter = require('events');

const emitter = new EventEmitter();

function eventListener() {
  console.log('Event occurred!');
}

emitter.on('event', eventListener);
emitter.on('event', eventListener); // Duplicate registration

// The eventListener will be called twice when the 'event' occurs,
// which may lead to unexpected behavior and potential memory leaks
```

To prevent duplicate registrations and the associated risks, ensure that event listeners are registered only once:

```node
emitter.on('event', eventListener);

// Before registering the event listener, check if it's already registered
if (!emitter.listenerCount('event', eventListener)) {
  emitter.on('event', eventListener);
}
```

- **Cyclic Reference:**

```node
const EventEmitter = require('events');

class MyObject {
  constructor() {
    this.emitter = new EventEmitter();
    this.emitter.on('event', this.eventHandler.bind(this)); // Creating a cyclic reference
  }

  eventHandler() {
    console.log('Event occurred!');
    // Perform some action with this.emitter or MyObject instance
  }

  cleanup() {
    this.emitter.removeListener('event', this.eventHandler);
    this.emitter = null; // Break the cyclic reference
  }
}

const obj = new MyObject();

// When the MyObject instance is no longer needed, call the cleanup method
obj.cleanup();
```

In this example, the `eventHandler` function is bound to this using bind(this), ensuring that it maintains a reference to the `MyObject` instance. However, this also creates a cyclic reference between `MyObject` and the `EventEmitter` instance. To break the cyclic reference, the `cleanup` method is introduced. It removes the event listener using `removeListener` and sets `this.emitter` to null, allowing the `MyObject` instance and the `EventEmitter` to be garbage collected properly.

## Explain the difference between EventEmitter and streams in Node.js

In Node.js, both `EventEmitter` and streams are important components for handling events and data. While they serve similar purposes, there are some key differences between the two:

1. Event Emitter:
   - The `EventEmitter` class is a core module in Node.js that allows you to handle and propagate events within an application.
   - It follows the observer pattern, where objects can subscribe to events and be notified when those events occur.
   - Events are emitted using the `emit` method and can be listened to using the `on` or `addListener` methods.
   - `EventEmitter` is typically used for handling custom events or building event-driven architectures.
   - It provides flexibility in creating and managing events, but it does not handle data streaming or buffering.

2. Streams:
   - Streams are a built-in mechanism in Node.js for efficiently handling large amounts of data in a streaming fashion.
   - Streams represent a sequence of data that can be read from or written to asynchronously, chunk by chunk.
   - There are several types of streams, such as Readable, Writable, Duplex, and Transform streams, each serving a specific purpose.
   - Streams are often used for tasks like reading from or writing to files, compressing or decompressing data, network communication, and more.
   - They provide backpressure handling, which allows the consumer of a stream to control the rate at which data is consumed to prevent overwhelming the system.

In summary, `EventEmitter` is primarily used for handling events and notifications within an application, whereas streams are used for processing and manipulating data in a streaming manner. While `EventEmitter` deals with events and their subscribers, streams handle data transfer in a controlled, chunk-by-chunk manner, allowing for efficient processing of large datasets.

## How does EventEmitter differ from the callback pattern in Node.js?

1. Callback Pattern:
   - The callback pattern is a common way of handling asynchronous operations in Node.js, where a function accepts a callback function as an argument.
   - The callback function is called once the asynchronous operation completes, providing the result or an error.
   - Callbacks are typically used for one-time notifications or single asynchronous operations.
   - When using callbacks, there is usually a single callback function that handles the result or error from the asynchronous operation.
   - Callbacks are often used in scenarios where there is a single expected response from an asynchronous operation.
   - Example use cases include file operations, network requests, and database queries.

In summary, the EventEmitter is a mechanism for handling multiple asynchronous events in an event-driven architecture, allowing multiple listeners to respond independently. On the other hand, the callback pattern is a way to handle a single asynchronous operation by providing a callback function to be executed once the operation completes.

## What are the limitations or performance considerations when using EventEmitter?

- **Memory Usage:** EventEmitter uses memory to store event listeners and their associated data. If you have a large number of listeners or emit events frequently, it can consume significant memory. This is especially important in scenarios where long-lived emitters or listeners are involved, as they can accumulate memory over time. It's important to properly manage and remove unnecessary listeners to avoid excessive memory usage.

```node
const EventEmitter = require('events');

// Create an instance of EventEmitter
const myEmitter = new EventEmitter();

// Attach a listener that consumes memory
myEmitter.on('event', () => {
  // Some memory-intensive operation
  const data = Buffer.alloc(1024 * 1024); // Allocating 1MB buffer
  // ...
});

// Emit the 'event' multiple times
for (let i = 0; i < 1000; i++) {
  myEmitter.emit('event');
}
```

In this example, we attach a listener to the 'event' event that allocates a 1MB buffer every time it's triggered. If this event is emitted frequently or if there are multiple events with such memory-intensive listeners, it can lead to excessive memory usage, potentially causing performance issues or even out-of-memory errors.

- **Event Loop Blocking:** EventEmitter is synchronous by default, meaning that when an event is emitted, all listeners for that event will be executed synchronously before the control is returned to the emitter. If any of the listeners take a significant amount of time to execute, it can block the event loop and impact the performance of other operations in your application. To mitigate this, ensure that your event listeners are designed to execute quickly and consider using asynchronous patterns within the listeners if necessary.

```node
const EventEmitter = require('events');

// Create an instance of EventEmitter
const myEmitter = new EventEmitter();

// Attach a listener that blocks the event loop
myEmitter.on('event', () => {
  // Long-running operation
  while (true) {
    // ...
  }
});

// Emit the 'event'
myEmitter.emit('event');

console.log('This line may never be reached!');
```

In this example, we attach a listener to the 'event' event that runs an infinite loop. When the event is emitted, the loop will block the event loop, preventing the execution of subsequent code. This can lead to poor performance and unresponsive behavior in your application.

- **Error Handling:** If an error occurs within an event listener, EventEmitter will throw an exception. If not handled properly, this can crash your application. It's important to wrap your event listeners in try-catch blocks to handle any potential errors and prevent them from propagating to the EventEmitter and crashing your application.

- **Garbage Collection:** EventEmitter relies on JavaScript's garbage collection to clean up unused event listeners. If listeners are not properly removed or references to listeners are not cleared, they can continue to consume memory even if they are no longer needed. To prevent memory leaks, it's crucial to remove event listeners when they are no longer needed or when the associated objects are no longer in use.

```node
const EventEmitter = require('events');

// Create an instance of EventEmitter
const myEmitter = new EventEmitter();

function setupListener() {
  myEmitter.on('event', () => {
    console.log('Event received!');
  });
}

// Call the setupListener function
setupListener();

// Emit the 'event'
myEmitter.emit('event');
```

In this example, we define a listener for the 'event' event inside the setupListener function. When setupListener is called, the listener is attached to the EventEmitter. However, if the listener is no longer needed or if the setupListener function is called multiple times, new listeners will be added to the EventEmitter without removing the previous ones. This can lead to memory leaks as the unused listeners are not garbage collected. To prevent this, it's essential to remove event listeners when they are no longer needed.

- **Performance Optimization:** If you have performance-critical scenarios, such as emitting a large number of events or having a significant number of listeners, you may need to optimize your code. Techniques like reducing unnecessary event emissions, using event batching or throttling, and optimizing listener execution can improve the overall performance of your application.

## What is event batching?

Event batching in event emitters refers to the technique of grouping multiple events together and emitting them as a batch, rather than emitting each event individually. This can provide performance benefits by reducing the overhead of emitting individual events and potentially optimizing event processing.

Here's an example to illustrate event batching in event emitters:

```node
const EventEmitter = require('events');

// Create an instance of EventEmitter
const myEmitter = new EventEmitter();

// Batch events and emit them after a certain interval
function batchEvents(events) {
  setTimeout(() => {
    // Emit the batched events
    for (const event of events) {
      myEmitter.emit(event.type, event.data);
    }
  }, 1000); // Emit every second
}

// Generate some events to be batched
const eventsToBatch = [
  { type: 'event1', data: 'Data for event 1' },
  { type: 'event2', data: 'Data for event 2' },
  { type: 'event3', data: 'Data for event 3' },
];

// Batch the events
batchEvents(eventsToBatch);
```

In this example, The batchEvents function takes an array of events and emits them as a batch after a fixed interval. By passing the events to be batched, we avoid emitting them individually. Once the interval elapses, the batched events are emitted one by one, allowing multiple listeners to respond accordingly. Event batching reduces overhead and optimizes event processing, especially in scenarios with a large number of events or frequent emissions.

## What is the maximum number of listeners that can be attached on a single event emitter instance?

In Node.js, the default maximum number of listeners that can be attached on event emitter instance is 10. This number can be modified using the `setMaxListeners` method with the number of listeners you want to modify it with
Additionally, you can set the maximum number of listeners to an unlimited value by passing 0 as the argument to `setMaxListeners`, effectively removing the limit on the number of listeners.

## How event emitters are different from Pub/Sub ques like SQS ( Amazon Simple Queue Service )?

Event emitters and publish/subscribe (pub/sub) queues, such as Amazon Simple Queue Service (SQS), have some similarities but serve different purposes and have distinct characteristics. Here are the key differences between event emitters and pub/sub queues:

1. **Communication Model**:- Event Emitter follow a rather direct communication model, where there will be an emitter which will emit some message and that message will be received by some subscribed listeners. While, In Pub/Sub queues publishers publish messages to a queue or topic, and subscribers receives message from these queues or topics.

2. **Message Persistance**:- Pub/Sub queues, like SQS persists message in the queue until they are not consumed by the subscribers or deleted explicitly. Queues allows subscriber to consume messages at their own pace. In contrast, event emitters do not persists messages by default, if there are no active listeners at the time when message will be published, they message will get lost.

3. **Message Ordering**:- Pub/sub queues, such as SQS, often guarantee message ordering within a single queue while event emitter do not provide inherent ordering guarantees.

4. **Scalability and Decoupling**:- : Pub/sub queues like SQS are designed to handle high-throughput scenarios and provide decoupling between publishers and subscribers. Publishers and subscribers can operate independently and at different rates. Event emitters, although they can be used for asynchronous communication, are often more tightly coupled since they directly trigger listeners within the same process.

5. **Message Delivery**:- Pub/sub queues, such as SQS, typically support reliable delivery guarantees. Messages are stored durably and can be delivered reliably to subscribers even in the presence of failures. Event emitters within a single process do not provide built-in mechanisms for reliable message delivery, as they rely on direct function calls and do not persist messages.

## What are streams in Node.js?

In Node.js, streams are a crucial concept that allows efficient handling of data flow. Streams are objects that facilitate the reading or writing of data sequentially, in small chunks, rather than loading the entire data into memory at once. This approach is particularly useful when dealing with large files or network communications.

Node.js provides several types of streams, categorized into four main categories: Readable, Writable, Duplex, and Transform streams.

```node
const fs = require('fs');

// Create a readable stream from a file
const readableStream = fs.createReadStream('source.txt');

let data = ''; // Variable to store the accumulated data

// Handle the 'data' event to process the data chunks
readableStream.on('data', (chunk) => {
  console.log('Received chunk of data:', chunk);
  data += chunk;
});

// Handle the 'end' event when the stream has finished reading
readableStream.on('end', () => {
  console.log('Finished reading the data.');
  console.log('Final data:', data);
});

// Handle the 'error' event if any error occurs during reading
readableStream.on('error', (err) => {
  console.error('An error occurred while reading the stream:', err);
});
```

In this example, we create a readable stream using `fs.createReadStream()` to read the contents of a file named 'source.txt'. We then handle three events:

1. **The 'data' event:** This event is emitted when a new chunk of data is available to be read from the stream. In the event handler, we log the received chunk to the console.

2. **The 'end' event:** This event is emitted when the stream has finished reading all the data. In the event handler, we log a message indicating that the data reading is complete.

3. **The 'error' event:** This event is emitted if any error occurs during the stream reading process. In the event handler, we log the error to the console.

## What is the purpose of using streams in node?

1. **Memory Efficiency:** Streams enable you to process data in smaller chunks instead of loading the whole dataset into memory. You can process it incrementally, making it possible to handle the large dataset which might not fit entirely into the memory.

2. **Performance and Speed:** By processing the data in smaller chunks, overall speed of the application can be increased. This is because stream allow for asynchronous processing, which means that while one chunk of the data is being processed, the next chunk can be move into the pipeline. This will minimize the waiting time for the data to be read from source or written to external sources.

3. **Backpressure and Flow Control:** Streams provide a mechanism for handling backpressure, Backpressure can occur if the reading side of a stream is slower than the writing side. Streams allow you to control the flow of data, ensuring that the data is processed at an optimal pace and preventing overwhelming the system or causing data loss.

4. **Compatibility and Modularity:** Streams are designed to be composable and modular. This makes it easier to chain or combine them together to perform complex data processing tasks involving data transformation, filtering or manipulation.

5. **File I/O and Network Operations:** Streams are particularly useful when dealing with file I/O operations or when working with network protocols. They allow you to efficiently read or write files, transmit data over network sockets, or process data from various sources such as HTTP requests or databases.
