---
title: "Third JWT Example"
slug: "third-jwt"
keywords: ["due", "jwt", "authentication", "token", "security"]
description: "Demonstrates JWT token-based authentication with Redis storage"
hide_title: true
---

# Third JWT Example

This example demonstrates how to implement JWT (JSON Web Token) authentication in Due framework with Redis-based token storage.

## Features

- JWT token generation and validation
- Redis-based token storage
- Token refresh mechanism
- Middleware integration
- User context extraction

## Project Structure

```
third-jwt/
├── main.go          # Main entry point
├── etc/
│   └── etc.toml     # Configuration file
└── jwt/
    ├── jwt.go       # JWT implementation
    └── store.go     # Redis token store
```

## Key Components

### JWT Initialization

```go
// Initialize JWT with Redis store
store := jwt.NewRedisStore(redisClient)
j := jwt.New(jwt.WithStore(store), jwt.WithSecret("your-secret-key"))
```

### Token Generation

```go
// Generate token for user
token, err := j.GenerateToken(uid, map[string]any{
    "username": "john",
    "role":     "admin",
})
```

### Token Validation

```go
// Validate and parse token
claims, err := j.ParseToken(tokenString)
if err != nil {
    // Invalid token
}
uid := claims.UID()
```

### Middleware Integration

```go
// JWT authentication middleware
func jwtAuth(middleware *node.Middleware, ctx node.Context) {
    token := ctx.Header("Authorization")
    claims, err := j.ParseToken(token)
    if err != nil {
        ctx.Response(&baseRes{Code: codes.Unauthorized.Code()})
        return
    }
    ctx.Set("uid", claims.UID())
    middleware.Next(ctx)
}

// Apply to routes
proxy.Router().Group(func(group *node.RouterGroup) {
    group.Middleware(jwtAuth)
    group.AddRouteHandler(getProfile, getProfileHandler)
})
```

### Token Refresh

```go
// Refresh token
newToken, err := j.RefreshToken(oldToken)
```

## Configuration

```toml
[jwt]
    secret = "your-secret-key"
    expiration = "24h"
    refreshExpiration = "7d"

[redis]
    addrs = ["127.0.0.1:6379"]
    db = 0
    prefix = "jwt"
```

## Running the Example

1. Ensure Redis is running on `127.0.0.1:6379`
2. Run:

```bash
go run main.go
```

## API Reference

- `jwt.New(opts...)` - Create JWT instance
- `j.GenerateToken(uid, claims)` - Generate token
- `j.ParseToken(token)` - Parse and validate token
- `j.RefreshToken(token)` - Refresh token
- `j.RevokeToken(token)` - Revoke token
- `j.IsRevoked(token)` - Check if token is revoked

## Use Cases

- User authentication
- API authorization
- Stateless session management
- Single sign-on (SSO)
- Microservice authentication

## Next Steps

- Try [bind-gate](../bind-gate/) to integrate JWT with gateway binding
- Try [third-redis](../third-redis/) for token storage optimization
