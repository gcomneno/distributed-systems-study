# Session 01 — What makes a system distributed?

Related issue: [#1](https://github.com/gcomneno/distributed-systems-study/issues/1)

## Status

In progress. Exercises and verification answers have not yet been reviewed.

## Learning objectives

At the end of this session, the learner should be able to:

- define a distributed system without reducing it to “more than one server”;
- identify local knowledge and unavailable global knowledge;
- explain partial failures;
- distinguish a timeout from proof that an operation failed;
- recognize the architectural consequences of uncertainty.

## Primary reference

Sukumar Ghosh, *Distributed Systems: An Algorithmic Approach*,
Second Edition.

Relevant sections:

- Chapter 1, Section 1.1 — What Is a Distributed System?
- Chapter 1, Section 1.4 — Important Issues in Distributed Systems
- Chapter 1, Section 1.5 — Common Subproblems
- Chapter 1, Section 1.7 — Parallel versus Distributed Systems
- Printed pages 3–13
- Chapter 2, Section 2.4.1 — Implementing RPC
- Printed page 31

The source book is not included in this repository.

## Core idea

A distributed system is not difficult merely because it contains several
machines.

It is difficult because autonomous processes:

- have separate address spaces;
- communicate through messages;
- observe only local state;
- experience delays;
- can fail independently;
- must still cooperate toward a shared result.

## Minimal characteristics

The reference text identifies four minimal characteristics.

### Multiple processes

More than one sequential process participates in the computation.

### Interprocess communication

Processes exchange messages, and message delivery takes time.

### Disjoint address spaces

One process cannot directly inspect or modify the private memory of another.

### Collective goal

The processes cooperate to produce a meaningful common result.

Several unrelated programs running on separate machines do not necessarily form
one distributed system.

## Local knowledge

A process can directly know:

- its own identity;
- its own local state;
- the messages it has received;
- information explicitly configured or communicated to it.

A process does not automatically know:

- the current state of another node;
- whether a message is delayed or lost;
- whether a remote node is slow or unavailable;
- whether a remote operation completed before communication failed;
- the exact global order of all events.

## Partial failure

In a local procedure call, caller and callee usually share the same machine and
much of the same failure context.

In a remote call, several outcomes can diverge:

- the request is never delivered;
- the server receives it but crashes before processing it;
- the server completes the operation but the response is lost;
- the operation is still running when the caller stops waiting;
- the caller retries and the server executes the logical operation twice.

The system can therefore be partially working and partially failing at the same
time.

## Timeout ambiguity

A timeout means:

> No response was observed within the selected waiting interval.

It does not prove:

> The remote operation did not execute.

A longer timeout changes how long the caller waits. It does not eliminate the
uncertainty.

## Backend example

Consider an order API.

```text
Client
  |
  | POST /orders
  v
Laravel API
  |
  | INSERT + COMMIT
  v
MySQL
  |
  | publish OrderCreated
  v
Message queue
```

Normal flow:

1. the client sends an order request;
2. the API validates it;
3. MySQL commits the order;
4. the API publishes an event;
5. the API returns `201 Created`;
6. a worker performs asynchronous work.

Failure flow:

1. MySQL commits the order;
2. the HTTP response is lost;
3. the client observes a timeout;
4. the client retries;
5. the system may create a duplicate order.

The client, API, database, and queue can each observe a different part of the
execution.

## Trade-off snapshot

| Problem | Option | Advantage | Disadvantage | Failure behavior |
|---|---|---|---|---|
| Remote uncertainty | Retry automatically | Improves chance of completion | May duplicate effects | Same logical operation may run again |
| Slow response | Increase timeout | Reduces premature timeouts | Increases waiting and resource use | Uncertainty remains |
| Duplicate request | Store an operation identifier | Enables deduplication | Requires durable state and rules | Retry can return the previous result |
| Database and queue update | Perform them independently | Simple implementation | Partial completion is possible | Order may exist without its event |

## Failure scenario

The Orders service requests a payment of 100 euros.

The payment provider completes the charge, but the response is lost.

The Orders service receives a timeout after five seconds.

Analyze:

- what the payment provider knows;
- what the Orders service knows;
- what the customer observes;
- whether a retry is safe;
- which component should preserve the logical identity of the payment;
- which guarantee can honestly be promised.

## Guided exercise

Design the behavior of:

```text
POST /orders
```

Assumptions:

1. the same logical request may arrive more than once;
2. MySQL may commit while the HTTP response is lost;
3. queue publication may fail after the database commit;
4. MySQL and the queue do not share an atomic transaction;
5. two orders for the same logical purchase are not acceptable.

Describe:

- which information the client sends;
- which durable data the server stores;
- how repeated attempts are recognized;
- which response is returned after a retry;
- what happens when the order exists but its event was not published;
- which guarantee is exposed to the client.

Do not provide code yet.

## Verification questions

### D1 — Conceptual

Which four minimal criteria characterize a distributed system in the reference
text?

### D2 — Comparison

What is the fundamental difference between a local call and a remote call when
the caller does not receive a result?

### D3 — Failure scenario

A service sends a payment request and receives a timeout. Which outcomes are
still possible?

### D4 — Practical analysis

An application contains:

- one PHP-FPM process;
- MySQL on another machine;
- Redis on a third machine;
- a worker on a fourth host.

Is it a distributed system under the four criteria? Justify each criterion.

### D5 — Interview question

Answer in no more than eight lines:

> Why are distributed systems harder than systems running on a single node?

Include:

- local knowledge;
- communication;
- partial failures;
- uncertainty;
- one architectural consequence.

## Stop point

Solutions and reviewed answers are intentionally absent.

Complete the exercise and questions before adding the final study sheet.
