[*配置GOPATH*](https://www.topgoer.com/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/%E9%85%8D%E7%BD%AEgopath.html)

# Golang 快速入门（Go Modules 与 Gin）

本文档简洁说明在 Go 模块模式下如何初始化项目、引入 gin 依赖、运行示例程序，并在国内环境下配置 Go Module 镜像以加速依赖下载。

> 现代 Go（>=1.11）推荐使用模块（Go Modules），已不再依赖 GOPATH。若需查看 GOPATH：

```bash
go env GOPATH
```

## 1. 初始化 Go 模块
在项目目录执行：

```bash
go mod init <module_name>
```

例如：

```bash
go mod init my-gin-app
```

该命令会创建 `go.mod`（并在需要时生成 `go.sum`），用于记录项目依赖。

## 2. 添加 gin 依赖
在模块模式下，用 `go get` 将依赖写入 `go.mod`：

```bash
go get github.com/gin-gonic/gin
```

说明：
- 使用 `-u` 会尝试升级到次要/修订版本：
  ```bash
  go get -u github.com/gin-gonic/gin
  ```
- 从 Go 1.17 起，安装可执行工具建议使用带版本的 `go install`：
  ```bash
  go install github.com/some/tool@latest
  ```

## 3. 示例项目结构与代码
建议目录结构：

```
my-gin-app/
  go.mod
  go.sum
  main.go
```

main.go 示例：

```go
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

func main() {
    r := gin.Default()

    r.GET("/ping", func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{"message": "pong"})
    })

    r.Run(":8080")
}
```

## 4. 运行与构建
- 运行：

```bash
go run main.go
```

- 构建：

```bash
go build -o my-gin-app
./my-gin-app
```

访问： http://localhost:8080/ping

## 5. 缓存与清理（可选）
- 模块缓存位置（通常）：

```bash
$(go env GOPATH)/pkg/mod  # 默认 GOPATH 为 ~/go
```

- 清空模块缓存：

```bash
go clean -modcache
```

- 清理构建缓存：

```bash
go clean -cache
```

## 6. 国内 Go Module 镜像（加速依赖下载）
在国内网络下，建议设置 GOPROXY 加速：

常用镜像：
- https://goproxy.cn
- https://goproxy.io
- https://goproxy.tuna.tsinghua.edu.cn

持久配置（推荐）：

```bash
# 使用国内镜像，遇不到包时回退直接访问源
go env -w GOPROXY=https://goproxy.cn,direct
# 可选：使用国内的 sumdb，避免默认 sumdb 访问被阻断
go env -w GOSUMDB=sum.golang.google.cn
```

临时命令指定：

```bash
GOPROXY=https://goproxy.cn,direct go get github.com/gin-gonic/gin
```

Windows（PowerShell）：

```powershell
go env -w GOPROXY="https://goproxy.cn,direct"
go env -w GOSUMDB="sum.golang.google.cn"
```

注意：
- 私有仓库请配合 `GOPRIVATE` / `GONOSUMDB` 使用，例如：
  ```bash
  go env -w GOPRIVATE=git.mycompany.com
  ```
- 确保私有仓库的凭据/证书已正确配置。

## 7. 常见问答（简明）
- “go get -u github.com/gin-gonic/gin 是局部还是全局？”
  - 依赖信息会记录在当前模块的 `go.mod`/`go.sum`（即“局部”），但下载的源码会缓存在本地模块缓存（复用于其它项目），不会写到全局项目文件。

- “如何更新依赖到最新？”
  - 使用 `go get -u <module>`，或在 `go.mod` 中调整版本后运行 `go mod tidy`。

---
