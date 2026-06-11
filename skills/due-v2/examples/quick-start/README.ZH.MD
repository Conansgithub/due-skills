---
title: "快速入门示例"
slug: "quick-start"
keywords: ["due", "快速入门", "节点", "基础", "入门"]
description: "Due 框架节点服务器的基础设置和路由处理器"
hide_title: true
---

# 快速入门示例

本示例演示了 Due 框架节点服务器的基础设置，包含简单的路由处理器。

## 特性

- 节点组件初始化
- Redis 定位器用于用户位置追踪
- Consul 注册中心用于服务发现
- 基础路由处理器实现
- JSON 消息编解码

## 项目结构

```
quick-start/
├── main.go          # 主入口文件
└── etc/
    └── etc.toml     # 配置文件
```

## 核心组件

### 主应用程序

```go
// 创建容器
container := due.NewContainer()
// 创建用户定位器
locator := redis.NewLocator()
// 创建服务发现
registry := consul.NewRegistry()
// 创建节点组件
component := node.NewNode(
    node.WithLocator(locator),
    node.WithRegistry(registry),
)
```

### 路由处理器

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

## 配置说明

编辑 `etc/etc.toml` 来配置：

- **集群节点**：实例 ID、名称、RPC 地址、编解码器
- **Redis 定位器**：连接地址、数据库、认证信息
- **Consul 注册中心**：服务器地址、健康检查设置
- **数据包**：字节序、路由/序列号/消息大小
- **日志**：级别、格式、输出目标

## 运行示例

1. 确保 Redis 运行在 `127.0.0.1:6379`
2. 确保 Consul 运行在 `127.0.0.1:8500`
3. 运行服务器：

```bash
go run main.go
```

## 测试

使用 client-tcp 或 client-ws 示例连接到本服务器并发送消息。

## 下一步

- 尝试 [gate-tcp](../gate-tcp/) 添加 TCP 网关
- 尝试 [bind-gate](../bind-gate/) 实现用户认证
- 尝试 [actor-model](../actor-model/) 进行复杂状态管理
