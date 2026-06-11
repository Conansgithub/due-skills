---
title: "Config File Example"
slug: "config-file"
keywords: ["due", "config", "file", "toml", "yaml"]
description: "Demonstrates file-based configuration management in Due framework"
hide_title: true
---

# Config File Example

This example demonstrates how to use the file-based configuration source in Due framework.

## Features

- File configuration source initialization
- TOML/YAML/JSON format support
- Configuration reading and watching
- Multiple configuration files

## Project Structure

```
config-file/
├── main.go                 # Main entry point
├── etc/
│   └── etc.toml            # Framework configuration
└── config/
    └── config.toml         # Application configuration
```

## Key Components

### Configuration Source

```go
// Set file configuration source
config.SetSource(file.NewSource(
    file.WithPath("config"),
))
```

### Reading Configuration

```go
// Get configuration value
value := config.Get("database.host")
port := config.GetInt("server.port")
debug := config.GetBool("app.debug")
```

### Watching Configuration

```go
// Watch for configuration changes
config.Watch("database", func(event config.Event) {
    log.Infof("config changed: %v", event)
})
```

## Configuration Format

```toml
[database]
    host = "127.0.0.1"
    port = 3306
    name = "mydb"
    user = "root"

[server]
    port = 8080
    timeout = "30s"

[app]
    debug = true
    name = "my-app"
```

## Running the Example

```bash
go run main.go
```

## API Reference

- `config.SetSource(source Source)` - Set configuration source
- `config.Get(key)` - Get value (returns `Value` interface)
- `config.GetInt/GetString/GetBool` - Get typed values
- `config.Has(key)` - Check if key exists
- `config.Watch(key, handler)` - Watch for changes
- `config.Load()` - Reload configuration
- `config.Store(file, content)` - Save configuration

## Use Cases

- Application configuration management
- Multi-environment configuration (dev/test/prod)
- Dynamic configuration reloading
- Configuration hot-updates

## Next Steps

- Try [config-file](../config-file/) with different formats (YAML/JSON)
- Try [eventbus-nats](../eventbus-nats/) for configuration change events
