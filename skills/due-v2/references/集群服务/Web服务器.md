# Web服务器 (Web)

## 概述

Web服务器是 Due 框架的HTTP服务组件，基于 Fiber 框架二次封装，提供高性能的Web服务能力。支持跨域、Swagger文档、中间件等特性。

## 包路径

```go
import "github.com/dobyte/due/component/http/v2"
```

## 配置参数

```toml
[http]
    # 服务器名称
    name = "http"
    # 服务器监听地址，默认为:8080
    addr = ":8080"
    # 是否启用控制台输出，默认为false
    console = false
    # body大小，支持单位： B | K | KB | M | MB | G | GB | T | TB | P | PB | E | EB | Z | ZB，默认为4M
    bodyLimit = "4M"
    # 最大并发连接数，默认为262144
    concurrency = 262144
    # 是否启用严格路由模式，默认为false
    strictRouting = false
    # 是否区分路由大小写，默认为false
    caseSensitive = false
    # 秘钥文件
    keyFile = ""
    # 证书文件
    certFile = ""
    
    # 跨域配置
    [http.cors]
        # 是否启用跨域
        enable = false
        # 允许跨域的请求源
        allowOrigins = []
        # 允许跨域的请求方法
        allowMethods = []
        # 允许跨域的请求头部
        allowHeaders = []
        # 当允许所有源时，是否允许携带凭据
        allowCredentials = false
        # 允许暴露给客户端的头部
        exposeHeaders = []
        # 浏览器缓存预检请求结果的时间
        maxAge = 0
        # 是否允许来自私有网络的请求
        allowPrivateNetwork = false
    
    # swagger文档配置
    [http.swagger]
        # 是否启用文档
        enable = true
        # API文档标题
        title = "API文档"
        # URL访问基础路径
        basePath = "/swagger"
        # swagger文件路径
        filePath = "./docs/swagger.json"
        # swagger-ui-bundle.js地址
        swaggerBundleUrl = ""
        # swagger-ui-standalone-preset.js地址
        swaggerPresetUrl = ""
        # swagger-ui.css地址
        swaggerStylesUrl = ""
```

## 核心 API

### 创建Web服务器

```go
// 创建Web服务器
func NewServer(opts ...Option) *Server

// 选项函数
func WithMiddlewares(middlewares ...interface{}) Option
```

### 服务器代理

```go
type Proxy struct {
    // 获取路由器
    Router() *Router
}
```

### 路由器

```go
type Router struct {
    // 注册GET路由
    Get(path string, handler Handler)
    // 注册POST路由
    Post(path string, handler Handler)
    // 注册PUT路由
    Put(path string, handler Handler)
    // 注册DELETE路由
    Delete(path string, handler Handler)
    // 注册PATCH路由
    Patch(path string, handler Handler)
}
```

### 上下文

```go
type Context interface {
    // 绑定请求参数
    Bind() *Binder
    // 成功响应
    Success(data interface{}) error
    // 失败响应
    Failure(code codes.Code) error
}
```

## 使用示例

### 基础Web服务器

```go
package main

import (
    "fmt"
    "github.com/dobyte/due/component/http/v2"
    "github.com/dobyte/due/v2"
    "github.com/dobyte/due/v2/codes"
    "github.com/dobyte/due/v2/log"
    "github.com/dobyte/due/v2/utils/xtime"
)

// @title API文档
// @version 1.0
// @host localhost:8080
// @BasePath /
func main() {
    // 创建容器
    container := due.NewContainer()
    // 创建HTTP组件
    component := http.NewServer()
    // 初始化应用
    initApp(component.Proxy())
    // 添加网格组件
    container.Add(component)
    // 启动容器
    container.Serve()
}

// 初始化应用
func initApp(proxy *http.Proxy) {
    // 路由器
    router := proxy.Router()
    // 注册路由
    router.Get("/greet", greetHandler)
}

// 请求
type greetReq struct {
    Message string `json:"message"`
}

// 响应
type greetRes struct {
    Message string `json:"message"`
}

// 路由处理器
// @Summary 测试接口
// @Tags 测试
// @Schemes
// @Accept json
// @Produce json
// @Param request body greetReq true "请求参数"
// @Response 200 {object} http.Resp{Data=greetRes} "响应参数"
// @Router /greet [get]
func greetHandler(ctx http.Context) error {
    req := &greetReq{}

    if err := ctx.Bind().JSON(req); err != nil {
        return ctx.Failure(codes.InvalidArgument)
    }

    log.Info(req.Message)

    return ctx.Success(&greetRes{
        Message: fmt.Sprintf("I'm server, and the current time is: %s", xtime.Now().Format(xtime.DateTime)),
    })
}
```

### 跨域配置

```toml
[http.cors]
    enable = true
    allowOrigins = ["http://localhost:3000"]
    allowMethods = ["GET", "POST", "PUT", "DELETE"]
    allowHeaders = ["Content-Type", "Authorization"]
    allowCredentials = true
    maxAge = 3600
```

### Swagger文档

1. 安装 swag

```bash
$ go install github.com/swaggo/swag/cmd/swag@latest
```

2. 生成 swagger 文件

```bash
$ swag init --parseDependency
$ rm -rf ./docs/docs.go
```

3. 配置 Swagger

```toml
[http.swagger]
    enable = true
    title = "API文档"
    basePath = "/swagger"
    filePath = "./docs/swagger.json"
```

### 中间件

```go
package main

import (
    "github.com/dobyte/due/component/http/v2"
    "github.com/gofiber/fiber/v2"
)

func main() {
    // 创建HTTP组件并添加中间件
    component := http.NewServer(
        http.WithMiddlewares(
            loggerMiddleware,
            authMiddleware,
        ),
    )
    
    // ...
}

// 日志中间件
func loggerMiddleware(ctx *fiber.Ctx) error {
    log.Infof("Request: %s %s", ctx.Method(), ctx.Path())
    return ctx.Next()
}

// 认证中间件
func authMiddleware(ctx *fiber.Ctx) error {
    token := ctx.Get("Authorization")
    if token == "" {
        return ctx.Status(401).JSON(fiber.Map{
            "error": "Unauthorized",
        })
    }
    return ctx.Next()
}
```

## 最佳实践

1. **路由设计**:
   - 使用RESTful风格设计API
   - 合理组织路由层级

2. **跨域处理**:
   - 生产环境明确指定允许的源
   - 合理设置预检请求缓存时间

3. **文档管理**:
   - 使用Swagger自动生成API文档
   - 保持文档与代码同步

4. **中间件**:
   - 合理使用中间件处理通用逻辑
   - 注意中间件执行顺序

5. **性能优化**:
   - 合理设置body大小限制
   - 根据服务器配置调整并发数

## 相关文件

- Web源码：`github.com/dobyte/due/component/http/`
- 配置示例：`examples/web-example/etc/etc.toml`
- Fiber文档：https://docs.gofiber.io/
