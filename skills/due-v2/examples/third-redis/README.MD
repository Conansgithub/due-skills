---
title: "Third Redis Example"
slug: "third-redis"
keywords: ["due", "redis", "client", "cache", "nosql"]
description: "Demonstrates Redis client integration for high-performance data operations"
hide_title: true
---

# Third Redis Example

This example demonstrates how to integrate the Redis client in Due framework for high-performance key-value operations.

## Features

- Redis client initialization
- Single/Cluster/Sentinel mode support
- Connection pool configuration
- TLS support
- Custom command execution
- Pub/Sub support

## Project Structure

```
third-redis/
├── main.go          # Main entry point
├── etc/
│   └── etc.toml     # Configuration file
└── redis/
    └── redis.go     # Redis client wrapper
```

## Key Components

### Client Initialization

```go
// Single node
client := redis.NewClient(&redis.Options{
    Addr:     "127.0.0.1:6379",
    Password: "",
    DB:       0,
})

// Cluster
client := redis.NewClusterClient(&redis.ClusterOptions{
    Addrs: []string{"127.0.0.1:7000", "127.0.0.1:7001"},
})
```

### Basic Operations

```go
// Set
client.Set(ctx, "key", "value", 0)

// Get
val, err := client.Get(ctx, "key").Result()

// Hash
client.HSet(ctx, "user:1", "name", "john")
client.HGet(ctx, "user:1", "name")

// List
client.LPush(ctx, "queue", "item1", "item2")
client.RPop(ctx, "queue")
```

### Pub/Sub

```go
// Subscribe
pubsub := client.Subscribe(ctx, "channel")
msg, err := pubsub.ReceiveMessage(ctx)

// Publish
client.Publish(ctx, "channel", "message")
```

## Configuration

```toml
[redis]
    addrs = ["127.0.0.1:6379"]
    password = ""
    db = 0
    poolSize = 10
    minIdleConns = 5
    maxRetries = 3
```

## Running the Example

1. Ensure Redis is running on `127.0.0.1:6379`
2. Run:

```bash
go run main.go
```

## API Reference

- `redis.NewClient(opts)` - Create single-node client
- `redis.NewClusterClient(opts)` - Create cluster client
- `client.Set/Get/Del` - String operations
- `client.HSet/HGet/HDel` - Hash operations
- `client.LPush/LPop/RPush/RPop` - List operations
- `client.SAdd/SMembers/SRem` - Set operations
- `client.Subscribe/Publish` - Pub/Sub operations

## Use Cases

- Session storage
- Real-time leaderboards
- Rate limiting
- Message queues
- Caching layer
- Distributed counters

## Next Steps

- Try [cache-redis](../cache-redis/) for cache abstraction
- Try [lock-redis](../lock-redis/) for distributed locking
