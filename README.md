# Eventra

**Distributed Event Delivery Platform**

Eventra is a backend infrastructure project focused on reliable webhook delivery and asynchronous event processing.

When applications communicate with external services through webhooks, things don't always go as expected. The receiving server might be unavailable, requests might time out, or the same event might be delivered more than once.

Eventra aims to handle these situations through a centralized delivery system with background processing, retry scheduling, rate limiting and delivery tracking.

The idea is simple: applications should be able to send events without having to worry about managing every delivery attempt themselves.

## Why Eventra?

Consider an application that needs to notify multiple external services whenever an order is placed or a payment is completed.

Sending HTTP requests directly from the main application can create several problems:

- A slow external service can affect the application's response time.
- Temporary failures can result in missed notifications.
- Uncontrolled retries can create duplicate events.
- Sending too many requests at once can overwhelm receiving services.
- Tracking failed deliveries and debugging issues becomes difficult.

Eventra is designed to separate event creation from event delivery, allowing applications to process events independently of the services receiving them.

## Key Features

### Event Ingestion
- REST APIs for accepting incoming events.
- Payload validation and request authentication.
- Unique identifiers for event tracking.
- Persistent event storage.

### Asynchronous Processing
- Background event processing.
- Queue-based event distribution.
- Concurrent delivery workers.
- Separation of API requests from delivery operations.

### Reliable Delivery
- Configurable retry policies.
- Exponential backoff with jitter.
- Delivery attempt tracking.
- Timeout and failure handling.
- Dead-letter queue for events that exhaust their retry attempts.

### Duplicate Prevention
- Idempotency keys.
- Duplicate event detection.
- Consistent event tracking across delivery attempts.

### Rate Limiting
- Per-endpoint rate limits.
- Redis-based distributed rate limiting.
- Controlled delivery concurrency.

### Event Recovery
- Manual replay of failed events.
- Inspection of previous delivery attempts.
- Configurable retry limits.

### Monitoring and Observability
- Event processing statistics.
- Delivery success and failure rates.
- Request latency tracking.
- Queue and worker monitoring.

## Tech Stack

| Component | Technology |
|---|---|
| Backend | Python, FastAPI |
| Database | PostgreSQL |
| Message Broker | Apache Kafka |
| Caching | Redis |
| Background Processing | Python workers |
| Frontend | React |
| Containerization | Docker |
| Monitoring | Prometheus, Grafana |
| Testing | Pytest, Locust |

## Architecture

Eventra follows an event-driven architecture where event ingestion and webhook delivery are handled independently.

```text
       Client Application
               |
               v
          Eventra API
               |
               v
       Request Validation
               |
               v
         Event Storage
               |
               v
         Message Queue
               |
               v
       Delivery Workers
               |
               v
      External Webhook URL
               |
         +-----+-----+
         |           |
      Success      Failure
         |           |
         v           v
      Delivered   Retry Queue
                      |
                 Retry Attempts
                      |
                 +----+----+
                 |         |
              Success   Exhausted
                 |         |
                 v         v
             Delivered  Dead Letter
```

## Project Structure

```text
eventra/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── services/
│   │   └── main.py
│   │
│   └── tests/
│
├── frontend/
│
├── infrastructure/
│   ├── docker/
│   └── monitoring/
│
├── docs/
│   ├── architecture/
│   └── benchmarks/
│
├── scripts/
│
├── .env.example
├── .gitignore
├── docker-compose.yml
└── README.md
```

## How It Works

1. A client application submits an event through the Eventra API.
2. The API validates the request and stores the event.
3. The event is added to the processing queue.
4. A background worker picks up the event.
5. The worker sends an HTTP request to the registered webhook endpoint.
6. The delivery result is recorded.
7. Failed deliveries are scheduled for retry according to the configured policy.
8. Events that exhaust their retry attempts can be inspected and replayed.

## Local Development

Eventra is being developed as a modular backend application with Docker-based infrastructure.

Local setup instructions and environment configuration will be documented alongside the initial implementation.

## Engineering Focus

A major part of Eventra is understanding how distributed systems behave when individual components fail.

Some of the problems being explored include:

- Handling worker crashes during event delivery.
- Preventing duplicate event processing.
- Managing retries without overwhelming external services.
- Processing events concurrently and safely.
- Handling unavailable webhook endpoints.
- Measuring throughput and delivery latency under load.

The goal is to build a system that is not only functional but also reliable, observable and easy to reason about.

---

**Eventra** is an ongoing engineering project focused on event-driven architecture, backend infrastructure and reliable distributed processing.
