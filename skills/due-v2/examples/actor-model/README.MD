---
title: "Actor Model Example"
slug: "actor-model"
keywords: ["due", "actor", "concurrency", "node", "game-server"]
description: "Demonstrates the Actor concurrency model in Due framework with room-based game logic"
hide_title: true
---

# Actor Model Example

This example demonstrates how to use the Actor concurrency model in Due framework to build a room-based game server with complex state management.

## Features

- Actor-based concurrency for game room management
- Middleware authentication with JWT-like token verification
- Room creation, joining, and message broadcasting
- Processor pattern for room lifecycle management
- Structured application layout (app/logic/middleware/route)

## Project Structure

```
actor-model/
├── main.go                    # Main entry point
├── etc/
│   └── etc.toml               # Configuration file
└── app/
    ├── app.go                 # Application initialization
    ├── base/
    │   └── base.go            # Base processor with common fields
    ├── logic/
    │   ├── core.go            # Core game logic
    │   ├── protocol.go        # Request/response message definitions
    │   ├── room.go            # Room management
    │   └── room_processor.go  # Room Actor processor
    ├── middleware/
    │   └── auth.go            # Authentication middleware
    └── route/
        └── route.go           # Route registration
```

## Key Components

### Actor Processor

```go
type RoomProcessor struct {
    base.BaseProcessor
    room *Room
}

func (p *RoomProcessor) Init(ctx node.Context) {
    // Room initialization
    p.room = NewRoom()
}

func (p *RoomProcessor) Start(ctx node.Context) {
    // Start room processing
}

func (p *RoomProcessor) Destroy(ctx node.Context) {
    // Cleanup
}
```

### Actor Registration

```go
proxy.Router().AddRouteHandler(createRoom, createRoomHandler)
proxy.Router().AddRouteHandler(joinRoom, joinRoomHandler, node.AuthorizedRoute)
proxy.Router().AddRouteHandler(sendMessage, sendMessageHandler, node.StatefulRoute)
```

### Message Broadcasting

```go
ctx.Broadcast(&cluster.MulticastArgs{
    UIDs: room.GetMembers(),
    Message: &cluster.Message{
        Route: broadcastRoute,
        Data:  msg,
    },
})
```

## Configuration

Edit `etc/etc.toml` to configure:

- **Cluster Node**: Instance ID, name, RPC address
- **Redis Locator**: Connection address for user location tracking
- **Consul Registry**: Service discovery settings
- **Packet**: Message format settings
- **Logging**: Level, format, output

## Running the Example

1. Ensure Redis is running on `127.0.0.1:6379`
2. Ensure Consul is running on `127.0.0.1:8500`
3. Run the server:

```bash
go run main.go
```

## Use Cases

- Multiplayer game rooms (card games, board games)
- Chat rooms with state management
- Real-time collaborative applications
- Any scenario requiring isolated state per user group

## Next Steps

- Try [bind-node](../bind-node/) to understand stateful routing
- Try [eventbus-nats](../eventbus-nats/) for cross-server events
