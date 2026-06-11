---
title: "Bind Node Example"
slug: "bind-node"
keywords: ["due", "bind-node", "stateful-routing", "node"]
description: "Demonstrates node binding and stateful routing in Due framework"
hide_title: true
---

# Bind Node Example

This example demonstrates how to bind users to specific node servers for stateful routing in Due framework.

## Features

- Node binding (NID/UID relationship)
- Stateful routing for user-specific data
- Middleware authentication
- Sequential route handlers (login → join → play)

## Project Structure

```
bind-node/
├── main.go          # Main entry point
└── etc/
    └── etc.toml     # Configuration file
```

## Key Components

### Node Binding

```go
func joinHandler(ctx node.Context) {
    req := &joinReq{}
    res := &joinRes{}
    ctx.Defer(func() { ctx.Response(res) })

    ctx.Parse(req)

    // Execute join game operation
    if err := doJoin(ctx.UID(), req); err != nil {
        res.Code = codes.Convert(err).Code()
        return
    }

    // Bind user to this node server
    if err := ctx.BindNode(ctx.UID()); err != nil {
        log.Errorf("bind node failed: %v", err)
        res.Code = codes.InternalError.Code()
        return
    }

    res.Code = codes.OK.Code()
}
```

### Stateful Route

```go
// Register stateful route - messages will be routed to the bound node
proxy.Router().AddRouteHandler(play, playHandler, node.StatefulRoute)
```

### Middleware Authentication

```go
func auth(middleware *node.Middleware, ctx node.Context) {
    if ctx.UID() == 0 {
        ctx.Response(&baseRes{Code: codes.Unauthorized.Code()})
    } else {
        middleware.Next(ctx)
    }
}
```

## Configuration

Edit `etc/etc.toml` to configure:

- **Cluster Node**: Instance ID, name, weight for load balancing
- **Redis Locator**: User location tracking
- **Consul Registry**: Service discovery
- **Packet**: Message format
- **Logging**: Output settings

## Running the Example

1. Start a gateway server (gate-tcp or gate-ws)
2. Start this node server
3. Ensure Redis and Consul are running
4. Run:

```bash
go run main.go
```

5. Test the flow:
   - Login (binds to gateway)
   - Join room (binds to node)
   - Play game (stateful routing)

## Use Cases

- Game room management with stateful logic
- Player data caching on specific nodes
- Load balancing with user affinity
- Distributed game state management

## Next Steps

- Try [actor-model](../actor-model/) for Actor-based concurrency
- Try [mesh-grpc](../mesh-grpc/) for microservice communication
