# MCP 浏览器工具配置说明

## 1. 概述

当前配置启用了两个 MCP 工具：

- **chrome-devtools-mcp**
- **@playwright/mcp**

它们都用于浏览器相关操作，但侧重点不同：

- **chrome-devtools-mcp**：偏向浏览器调试、页面分析、DevTools 协议能力接入。
- **@playwright/mcp**：偏向浏览器自动化、页面操作和测试执行。

---

## 2. 配置内容

```toml
[mcp_servers.chrome-devtools]
type = "stdio"
command = "cmd"
args = ["/c", "npx", "-y", "chrome-devtools-mcp@latest", "--autoConnect"]
startup_timeout_ms = 20000
enabled = true

[mcp_servers.playwright]
command = "npx"
args = [
  "-y",
  "@playwright/mcp@latest",
  "--browser", "chromium",
  "--user-data-dir", "D:/Playwright-chrome-userdata"
]
enabled = true
startup_timeout_sec = 30
tool_timeout_sec = 120
```

---

## 3. 工具介绍

### 3.1 chrome-devtools-mcp

`chrome-devtools-mcp` 是基于 **Chrome DevTools Protocol** 的 MCP 服务，主要用于连接和控制浏览器调试能力。

**特点：**
- 支持自动连接浏览器
- 适合页面调试、DOM 分析、网络请求排查
- 更适合开发和问题定位场景

**当前配置说明：**
- 通过 `cmd /c npx` 启动
- 使用 `--autoConnect` 自动连接浏览器
- 启动超时为 `20000ms`
- 当前状态为启用

---

### 3.2 @playwright/mcp

`@playwright/mcp` 是基于 **Playwright** 的 MCP 服务，主要用于浏览器自动化操作。

**特点：**
- 支持页面打开、点击、输入、跳转等自动化能力
- 适合测试、流程执行和网页交互场景
- 支持指定浏览器及用户数据目录

**当前配置说明：**
- 通过 `npx` 启动
- 指定浏览器为 `chromium`
- 使用 `D:/Playwright-chrome-userdata` 作为用户数据目录
- 启动超时为 `30s`
- 单次工具执行超时为 `120s`
- 当前状态为启用

---

## 4. 配置说明

### chrome-devtools

| 配置项 | 说明 |
|---|---|
| `type = "stdio"` | 通过标准输入输出与宿主通信 |
| `command = "cmd"` | 使用 Windows 命令行启动 |
| `args` | 执行 `chrome-devtools-mcp` 并自动连接 |
| `startup_timeout_ms = 20000` | 启动超时 20 秒 |
| `enabled = true` | 启用该服务 |

### playwright

| 配置项 | 说明 |
|---|---|
| `command = "npx"` | 直接通过 npx 启动 |
| `args` | 启动 Playwright MCP，并指定 Chromium |
| `--user-data-dir` | 指定浏览器用户数据目录，保留登录态等信息 |
| `startup_timeout_sec = 30` | 启动超时 30 秒 |
| `tool_timeout_sec = 120` | 单次操作最长 120 秒 |
| `enabled = true` | 启用该服务 |

---

## 5. 两个工具对比

| 对比项 | chrome-devtools-mcp | @playwright/mcp |
|---|---|---|
| 核心定位 | 浏览器调试 | 浏览器自动化 |
| 主要用途 | 页面分析、调试、排查问题 | 页面操作、流程执行、自动化测试 |
| 技术基础 | Chrome DevTools Protocol | Playwright |
| 适合场景 | 开发调试、网络/DOM 排查 | E2E 测试、自动化任务 |
| 浏览器连接方式 | 自动连接现有浏览器 | 启动并控制 Chromium |
| 用户数据目录 | 未单独指定 | 已指定本地目录 |

---

## 6. 结论

这份配置同时具备了**调试能力**和**自动化能力**：

- **chrome-devtools-mcp** 适合做浏览器调试和问题分析
- **@playwright/mcp** 适合做自动化操作和测试执行

两者结合后，可以覆盖大多数浏览器相关的开发、排查和自动化场景。

---
