---
title: "Quick Start Example"
slug: "quick-start"
keywords: ["due", "quick-start", "node", "basic", "getting-started"]
description: "Basic Due framework node server setup with route handlers"
hide_title: true
---

# Quick Start Example

This example demonstrates the basic setup of a Due framework node server with simple route handlers.

## Features

- Node component initialization
- Redis locator for user location tracking
- Consul registry for service discovery
- Basic route handler implementation
- JSON message encoding/decoding

## Project Structure

```
quick-start/
├── main.go          # Main entry point
└── etc/
    └── etc.toml     # Configuration file
```

## Key Components

### Main Application

```go
// Create container
container := due.NewContainer()
// Create user locator
locator := redis.NewLocator()
// Create service discovery
registry := consul.NewRegistry()
// Create node component
component := node.NewNode(
    node.WithLocator(locator),
    node.WithRegistry(registry),
)
```

### Route Handler

```go
const greet = 1

func greetHandler(ctx node.Context) {
    req := &greetReq{}
    res := &greetRes{}
    
    ctx.Defer(func() {
        ctx.Response(res)
    })
    
    ctx.Parse(req)
    res.Message = "Hello from server!"
}
```

## Configuration

Edit `etc/etc.toml` to configure:

- **Cluster Node**: Instance ID, name, RPC address, codec
- **Redis Locator**: Connection address, database, authentication
- **Consul Registry**: Server address, health check settings
- **Packet**: Byte order, route/sequence/message sizes
- **Logging**: Level, format, output destinations

## Running the Example

1. Ensure Redis is running on `127.0.0.1:6379`
2. Ensure Consul is running on `127.0.0.1:8500`
3. Run the server:

```bash
go run main.go
```

## Testing

Use the client-tcp or client-ws examples to connect and send messages to this server.

## Next Steps

- Try [gate-tcp](../gate-tcp/) to add a TCP gateway
- Try [bind-gate](../bind-gate/) to implement user authentication
- Try [actor-model](../actor-model/) for complex state management
