# Windows UTF-8 编码配置

> PowerShell 和 Python 的 UTF-8 快速配置指南

## Windows Terminal 通用设置

打开 Windows Terminal，按 `Ctrl + ,` 打开 settings.json，在 `profiles.defaults` 中添加：

```json
"profiles": {
  "defaults": {
    "encoding": "utf-8"
  }
}
```

此设置对所有 shell（PowerShell、CMD、WSL）生效。

## PowerShell 配置

以管理员身份打开 PowerShell，依次执行：

```powershell
set-executionpolicy remotesigned

if (!(Test-Path -Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}

$PROFILE
```

编辑打开的文件路径，添加：

```powershell
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding
```

重启 PowerShell，运行 `chcp` 验证（应显示 65001）。

## Python 配置

PowerShell 中执行：

```powershell
[Environment]::SetEnvironmentVariable("PYTHONUTF8", "1", "User")
```

或手动设置：右键"此电脑" → 属性 → 高级系统设置 → 环境变量
- 新建用户变量：`PYTHONUTF8` = `1`

## 验证

```powershell
# PowerShell
chcp

# Python
python -c "import sys; print(sys.stdout.encoding)"
```

## 常见问题

**配置后无效？** → 重启 PowerShell/Terminal/IDE

**Python 仍乱码？** → 确保文件头声明 `# -*- coding: utf-8 -*-`
