```toml
[mcp_servers.playwright]
command = "npx"
args = ["-y", "@playwright/mcp@latest", "--headless", "--browser", "chromium"]
enabled = true
startup_timeout_sec = 30
tool_timeout_sec = 120
```
