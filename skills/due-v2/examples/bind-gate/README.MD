---
title: "Bind Gate Example"
slug: "bind-gate"
keywords: ["due", "bind-gate", "authentication", "login", "node"]
description: "Demonstrates user authentication and gateway binding in Due framework"
hide_title: true
---

# Bind Gate Example

This example demonstrates how to implement user authentication and bind users to the gateway server in Due framework.

## Features

- User login authentication
- Gateway binding (GID/CID/UID relationship)
- Error handling with framework error codes
- JSON message encoding/decoding

## Project Structure

```
bind-gate/
├── main.go          # Main entry point with login handler
└── etc/
    └── etc.toml     # Configuration file
```

## Key Components

### Login Handler

```go
func loginHandler(ctx node.Context) {
    ctx.Task(func(ctx node.Context) {
        req := &loginReq{}
        res := &loginRes{}
        ctx.Defer(func() { ctx.Response(res) })

        ctx.Parse(req)

        // Verify credentials
        uid, err := doLogin(req)
        if err != nil {
            res.Code = codes.Convert(err).Code()
            return
        }

        // Bind user to gateway
        if err = ctx.BindGate(uid); err != nil {
            log.Errorf("bind gate failed: %v", err)
            res.Code = codes.InternalError.Code()
            return
        }

        res.Code = codes.OK.Code()
    })
}
```

### Gateway Binding

```go
// After successful authentication, bind the user to the gateway
if err := ctx.BindGate(uid); err != nil {
    // Handle binding failure
}
```

## Configuration

Edit `etc/etc.toml` to configure:

- **Cluster Node**: Instance settings
- **Redis Locator**: User location tracking
- **Consul Registry**: Service discovery
- **Packet**: Message format
- **Logging**: Output settings

## Running the Example

1. Start a gateway server (gate-tcp or gate-ws example)
2. Ensure Redis is running on `127.0.0.1:6379`
3. Ensure Consul is running on `127.0.0.1:8500`
4. Run the node server:

```bash
go run main.go
```

5. Connect a client and send login request:

```json
{
  "account": "fuxiao",
  "password": "123456"
}
```

## Use Cases

- User authentication systems
- Session management
- Gateway-based message routing
- Multi-server user tracking

## Next Steps

- Try [bind-node](../bind-node/) to bind users to specific node servers
- Try [third-jwt](../third-jwt/) for token-based authentication
