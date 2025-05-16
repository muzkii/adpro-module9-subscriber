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
