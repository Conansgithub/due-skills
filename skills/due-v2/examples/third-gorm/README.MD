---
title: "Third GORM Example"
slug: "third-gorm"
keywords: ["due", "gorm", "mysql", "database", "orm"]
description: "Demonstrates GORM database integration with MySQL for data persistence"
hide_title: true
---

# Third GORM Example

This example demonstrates how to integrate GORM (Go Object Relational Mapping) with MySQL in Due framework for database operations.

## Features

- GORM MySQL initialization
- Connection pool configuration
- Custom logger integration
- JSON serializer support
- Multi-instance support
- Auto-migration

## Project Structure

```
third-gorm/
├── main.go              # Main entry point
├── etc/
│   └── etc.toml         # Configuration file
└── gorm/
    ├── mysql.go         # MySQL initialization
    ├── logger.go        # Custom logger
    └── serializer/
        └── json.go      # JSON serializer
```

## Key Components

### Database Initialization

```go
// Initialize GORM with MySQL
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
    Logger: logger.New(),
})
if err != nil {
    log.Fatalf("failed to connect database: %v", err)
}
```

### Connection Pool

```go
// Configure connection pool
sqlDB, err := db.DB()
sqlDB.SetMaxIdleConns(10)
sqlDB.SetMaxOpenConns(100)
sqlDB.SetConnMaxLifetime(3600 * time.Second)
```

### Model Definition

```go
type User struct {
    ID        int64          `gorm:"primarykey"`
    Username  string         `gorm:"size:50;uniqueIndex"`
    Email     string         `gorm:"size:100"`
    Status    int            `gorm:"default:1"`
    Extra     datatypes.JSON `gorm:"serializer:json"`
    CreatedAt time.Time
    UpdatedAt time.Time
}
```

### CRUD Operations

```go
// Create
db.Create(&User{Username: "john", Email: "john@example.com"})

// Read
var user User
db.First(&user, "username = ?", "john")

// Update
db.Model(&user).Update("status", 2)

// Delete
db.Delete(&user)
```

## Configuration

```toml
[gorm.mysql]
    dsn = "root:password@tcp(127.0.0.1:3306)/mydb?charset=utf8mb4&parseTime=True&loc=Local"
    maxIdleConns = 10
    maxOpenConns = 100
    connMaxLifetime = "1h"
```

## Running the Example

1. Ensure MySQL is running on `127.0.0.1:3306`
2. Create database: `CREATE DATABASE mydb;`
3. Run:

```bash
go run main.go
```

## API Reference

- `gorm.Open(dialect, config)` - Open database connection
- `db.Create(value)` - Insert record
- `db.First(dest, conditions...)` - Query first record
- `db.Find(dest, conditions...)` - Query multiple records
- `db.Save(value)` - Update or insert
- `db.Delete(value)` - Delete record
- `db.AutoMigrate(models...)` - Auto-migrate schema

## Use Cases

- User data persistence
- Game state storage
- Transaction records
- Configuration management
- Audit logs

## Next Steps

- Try [third-redis](../third-redis/) for caching database queries
- Try [cache-redis](../cache-redis/) for high-performance data access
