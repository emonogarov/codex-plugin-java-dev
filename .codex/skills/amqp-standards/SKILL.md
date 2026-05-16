---
name: amqp-standards
description: Canonical AMQP messaging standards for services, code, configuration, contracts, and documentation across the system. Use when designing, implementing, reviewing, or documenting RabbitMQ exchanges, queues, bindings, routing keys, payloads, retries, DLQs, producers, or consumers.
---

# AMQP Standards

## Scope

- Treat this skill as the canonical AMQP standard for the system.
- Apply these rules to documentation, code, configuration, diagrams, and reviews.
- Do not assume existing implementations or existing docs already follow these standards.
- If an existing artifact conflicts with these standards, call out the mismatch explicitly and advise the standard-compliant form for new or changed work.
- If the task is to document current implemented behavior, keep the documented current state accurate and separately note the standard deviation when relevant.

## Protocol and compatibility

- Use RabbitMQ as the broker.
- Use maintained, stable client libraries for the implementation language.
- Do not break existing consumers within a major version.

### Compatibility rules

- Treat adding optional JSON fields as non-breaking within the same major version.
- Treat removing fields, renaming fields, or changing field types as breaking changes.
- Treat changing routing key meaning, event meaning, or command meaning as breaking changes.
- Treat reusing an existing routing key for different semantics as a breaking change.

## Topology and naming

- Use lowercase names only.
- Use `.` as the separator.
- Prefix names with the owning service.

### Exchange naming

- Shape: `{service}.{domain}.{exchangeCategory}.v{major}`
- `service`: owning service that creates and manages the exchange.
- `domain`: business domain or bounded context.
- `exchangeCategory`: message category (`events` or `commands`).
   - `events` for event streams published by the owning service.
   - `commands` for command streams published toward other services.
- Use a mandatory major version suffix on exchanges: `.v{major}`.
- Increment the exchange major version when the exchange contract breaks.

Examples:

- `order.orders.events.v1`
- `order.products.events.v1`
- `notifications.push.commands.v2`

### Queue naming

- Shape: `{service}.{domain}.{queuePurpose}.v{major}`
- `service`: owning consumer service and queue owner.
- `domain`: business domain or bounded context.
- `queuePurpose`: message category (`events` or `commands`).
  - `events` for queues that consume events from other services.
  - `commands` for queues that consume commands from other services.

Examples:

- `processor.orders.events.v1`
- `notifications.push.commands.v2`

### Routing keys

- Shape: `{domain}.{entity}.{action}.v{major}`

Examples:

- `orders.order.created.v1`
- `billing.invoice.paid.v2`
- `push.notification.send.v2`

### Binding documentation

- When documenting bindings in specs, diagrams, or reviews, use the identifier shape `{exchange}->{queue}:{routingKey}`.
- Always list the explicit routing key when documenting a binding.
- If multiple routing keys are bound, document one line per binding.

Examples:

- `order.orders.events.v1->processor.orders.events.v1:orders.order.created.v1`
- `notifications.push.commands.v2->notifications.push.commands.v2:push.notification.send.v2`

### Queue ownership

- Determine the queue owner from the `{service}` prefix in the queue name.
- If multiple services need the same messages, each service owns its own queue.
- Do not use shared queues.
- If a legacy shared queue exists, advise migration to per-service queues.

### Exchange types

- Use `topic` as the preferred default for event routing by routing key.
- Use `direct` only for explicit point-to-point routing.
- Avoid `fanout` unless broadcast without routing keys is truly required.

### Queue properties

- Use durable exchanges and durable queues for production traffic.
- Use auto-delete queues only for short-lived consumers and tests.

### Dead-letter mechanism

- Use a dead-letter exchange for every durable queue.
- Name dead-letter exchanges with the `.dlx` suffix: `{service}.{domain}.{exchangeCategory}.v{major}.dlx`.
- Name dead-letter queues with the `.dlq` suffix: `{service}.{domain}.{queuePurpose}.v{major}.dlq`.

Exchange examples:

- `order.orders.events.v1.dlx`
- `notifications.push.commands.v2.dlx`

Queue examples:

- `processor.orders.events.v1.dlq`
- `notifications.push.commands.v2.dlq`

## Message envelope

### Headers

- Require `messageId` for cross-service messages.
- Require `correlationId` when the message is part of a request, workflow, or causal chain.
- These headers may be added manually by the producer or automatically by observability or messaging tooling such as DataDog instrumentation.
- The developer remains responsible for ensuring the required headers exist regardless of whether they are added by application code or tooling.
- Standard library or platform metadata may be present.
- Optional Spring AMQP headers may include `__TypeId__`, `__ContentTypeId__`, and `__KeyTypeId__`.

### Payload

- Use content type `application/json`.
- Use UTF-8 encoded JSON.
- Use camelCase JSON object properties.

### Consumer bindings

- Bind each consumer to explicit routing keys.
- Avoid wildcard bindings unless the consumer is intentionally built to handle all variants.

## Operational limits

- Keep message size at or below 256 KB unless explicit approval exists.
- Use prefetch `10` as the starting default.
- Increase or decrease prefetch based on handler latency, memory usage, ordering sensitivity, and consumer concurrency.

## Working rules

- For new designs and new implementations, follow this standard directly.
- For reviews, flag deviations in naming, routing, ownership, compatibility, payload shape, exchange type, or queue durability.
- For contract-breaking changes, version the affected exchange, queue contract, or routing key major version instead of silently changing semantics.
- For documentation, include exchange, queue, routing key, binding, and payload details when they are relevant to the scope.
