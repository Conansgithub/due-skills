---
title: "EventBus NATS Example"
slug: "eventbus-nats"
keywords: ["due", "eventbus", "nats", "pubsub", "event-driven"]
description: "Demonstrates NATS-based event bus for distributed event-driven architecture"
hide_title: true
---

# EventBus NATS Example

This example demonstrates how to use the NATS-based event bus in Due framework for distributed event-driven communication.

## Features

- NATS event bus initialization
- Event publishing and subscribing
- Cross-server event propagation
- Topic-based event routing
- Automatic reconnection handling

## Project Structure

```
eventbus-nats/
├── main.go          # Main entry point
└── etc/
    └── etc.toml     # Configuration file
```

## Key Components

### Event Bus Initialization

```go
// Set global event bus to NATS
eventbus.SetEventbus(nats.NewEventbus())
```

### Publishing Events

```go
// Publish event to a topic
eventbus.Publish(context.Background(), "user.login", map[string]any{
    "uid":    1,
    "time":   time.Now().Unix(),
    "status": "success",
})
```

### Subscribing to Events

```go
// Subscribe to events
eventbus.Subscribe(context.Background(), "user.login", func(ctx context.Context, event eventbus.Event) error {
    log.Infof("received login event: uid=%d", event.Data()["uid"])
    return nil
})
```

### Unsubscribing

```go
// Unsubscribe from events
eventbus.Unsubscribe(context.Background(), "user.login", handler)
```

## Configuration

```toml
[eventbus.nats]
    addrs = ["127.0.0.1:4222"]
    username = ""
    password = ""
    maxReconnects = 60
    reconnectWait = "2s"
```

## Running the Example

1. Ensure NATS server is running on `127.0.0.1:4222`
2. Run:

```bash
go run main.go
```

## API Reference

- `eventbus.SetEventbus(eb Eventbus)` - Set global event bus
- `eventbus.Publish(ctx, topic, data)` - Publish event
- `eventbus.Subscribe(ctx, topic, handler)` - Subscribe to events
- `eventbus.Unsubscribe(ctx, topic, handler)` - Unsubscribe
- `eventbus.Close()` - Close event bus connection

## Use Cases

- Cross-server communication
- Distributed event processing
- Real-time notifications
- Audit logging
- Microservice coordination

## Next Steps

- Try [cache-redis](../cache-redis/) for distributed caching
- Try [mesh-grpc](../mesh-grpc/) for RPC-based communication
