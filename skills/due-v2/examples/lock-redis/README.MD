---
title: "Lock Redis Example"
slug: "lock-redis"
keywords: ["due", "lock", "redis", "distributed-lock", "concurrency"]
description: "Demonstrates Redis-based distributed locking for concurrent resource access"
hide_title: true
---

# Lock Redis Example

This example demonstrates how to use the Redis-based distributed lock in Due framework for managing concurrent access to shared resources.

## Features

- Redis lock initialization
- Acquire and release locks
- Try-acquire with timeout
- Automatic lock expiration
- Concurrent lock demonstration

## Project Structure

```
lock-redis/
├── main.go          # Main entry point
└── etc/
    └── etc.toml     # Configuration file
```

## Key Components

### Lock Initialization

```go
// Set global lock to Redis
lock.SetLock(redis.NewLock())
```

### Acquiring Locks

```go
// Acquire lock (blocks until acquired)
ctx := context.Background()
err := lock.Acquire(ctx, "resource:counter")
if err != nil {
    log.Errorf("acquire lock failed: %v", err)
    return
}
defer lock.Release(ctx, "resource:counter")
```

### Try-Acquire with Timeout

```go
// Try to acquire lock with timeout
err := lock.TryAcquire(ctx, "resource:counter", 5*time.Second)
if err != nil {
    log.Warnf("lock not available: %v", err)
    return
}
```

### Releasing Locks

```go
// Release lock
lock.Release(ctx, "resource:counter")
```

## Configuration

```toml
[lock.redis]
    addrs = ["127.0.0.1:6379"]
    db = 0
    password = ""
    maxRetries = 3
    prefix = "due:lock"
```

## Running the Example

1. Ensure Redis is running on `127.0.0.1:6379`
2. Run:

```bash
go run main.go
```

## API Reference

- `lock.SetLock(l Lock)` - Set global lock implementation
- `lock.Acquire(ctx, key)` - Acquire lock (blocking)
- `lock.TryAcquire(ctx, key, timeout)` - Try to acquire with timeout
- `lock.Release(ctx, key)` - Release lock
- `lock.IsLocked(ctx, key)` - Check if key is locked

## Use Cases

- Preventing duplicate operations
- Rate limiting
- Resource access control
- Leader election
- Distributed transaction coordination

## Next Steps

- Try [cache-redis](../cache-redis/) for distributed caching
- Try [eventbus-nats](../eventbus-nats/) for event coordination
