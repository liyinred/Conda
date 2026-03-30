# 永久修改 PowerShell 编码

> 通过添加 profile 文件，在 VSCode/IDEA 等开发工具中均生效。

## 步骤

### 1. 以管理员身份打开 PowerShell，依次执行以下命令
```powershell
# 允许运行自定义脚本
set-executionpolicy remotesigned

# 创建默认 profile（如果存在则不创建）
if (!(Test-Path -Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}

# 查看创建的 profile 文件位置
$PROFILE
```

### 2. 根据输出路径找到 profile 文件，例如
```
C:\Users\Yuanfei\Documents\PowerShell\Microsoft.PowerShell_profile.ps1
```

### 3. 用编辑器打开该文件，添加以下内容
```powershell
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding
```

### 4. 重启 PowerShell

运行 `chcp` 验证，编码已修改为 **65001**（即 UTF-8）。
