# Advanced Programming Rust 
by Andriyo Averill Fahrezi, NPM of 2306172325

## Module 9 - Software Architecture - Subscriber

### Understanding AMQP and Broker URL

#### What is AMQP?

AMQP stands for **Advanced Message Queuing Protocol**.  It is an open standard application layer protocol used for message-oriented middleware.  AMQP enables systems to exchange messages in a **reliable**, **asynchronous**, and **platform-independent** way.  It supports features like message queuing, routing, publish/subscribe, transactions, and security.  Popular message brokers like **RabbitMQ** use AMQP as their core protocol.

---

#### What does `guest:guest@localhost:5672` mean?

This is the **connection URL** used to connect to a RabbitMQ broker over AMQP.  
It follows the structure: 
```amqp://<username>:<password>@<host>:<port>```

##### Breakdown:
- `guest:guest`  
  - The first **`guest`** is the **username**.
  - The second **`guest`** is the **password**.
  - These are default credentials provided by RabbitMQ for local development.

- `localhost:5672`  
  - `localhost` refers to the local machine (127.0.0.1) where RabbitMQ is running.
  - `5672` is the **default port** RabbitMQ uses to communicate via the AMQP protocol.

##### Summary:
So, `amqp://guest:guest@localhost:5672` means:  
> “Connect to RabbitMQ running on my own machine (`localhost`), using username `guest` and password `guest`, over port `5672` using the AMQP protocol.”

### Simulation Slow Subscriber
![image](https://github.com/user-attachments/assets/bf745a75-2d50-4dab-95fe-2142d2e76efe)

As shown in the image above, there are 35 messages in the queue. This happened because I ran `cargo run` on the publisher 7 times in quick succession. This indicates that the number of messages in the queue increases based on how many messages the publisher sends.
In the `main.rs` file of the subscriber, we've added a `thread::sleep(_ten_millis);` line, which slows down the subscriber's processing rate by introducing a delay between each message. As a result, the subscriber now handles messages one at a time at a fixed interval.
Because of this delay, if the publisher continues to send more messages while the subscriber is still processing, those messages will begin to stack up in the queue. This leads to unprocessed messages accumulating until the subscriber catches up.

### Reflection and Running At Least Three Subscribers
![image](https://github.com/user-attachments/assets/318b5e51-c8ec-4317-a771-9857fae6cf8d)

As told, I ran 3 subscribers each in different terminals. When I ran `cargo run` on the publisher the same as before, 7 times in quick succession, turns out that the amount of messages in the queue have been reduced from initially 35 to 25 which is an improvement! Since this year I'm also taking ComptNetSec, turns out that this happened because RabbitMQ uses a work queue model with round-robin dispatch when multiple consumers (subscribers) are listening to the same queue. 
- Before: We had one slow subscriber with `thread::sleep(_ten_millis)`, so messages were piling up in the queue.
- After: We launched three subscribers, so now RabbitMQ distributes the messages evenly among all subscribers.

As a result, the messages were processed faster, and the total unprocessed messages in the queue dropped from 35 to 25 — meaning 10 were consumed quickly across the three subscribers.

### Improvements
Maybe some takeaways that we can use to improve the code that we have learned previously on the past tutorials, is that we can use `tokio::time::sleep()` instead of `std::thread::sleep()`. So, in `main.rs` on the Subscriber side, instead of 
```
  ...
    thread::sleep(_ten_millis);
  ...
```

We can use:
```
  ...
    tokio::time::sleep(Duration::from_millis(10)).await;
  ...
```
This way, since the code is inside an async context. `thread::sleep()` blocks the thread and wastes resources. So switching to use `tokio` is a good way to go.


