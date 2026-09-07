# Windows 环境 UTF-8 编码配置指南

> 通过配置 PowerShell 和 Python，在 VSCode/IDEA 等开发工具中实现 UTF-8 编码支持。

---

## 目录
- [PowerShell UTF-8 配置](#powershell-utf8-配置)
- [Python UTF-8 配置](#python-utf8-配置)
- [验证配置](#验证配置)
- [常见问题](#常见问题)

---

## PowerShell UTF-8 配置

### 步骤 1：允许运行自定义脚本

以管理员身份打开 PowerShell，执行以下命令：

```powershell
# 允许运行自定义脚本
set-executionpolicy remotesigned
```

### 步骤 2：创建 PowerShell Profile

执行以下命令创建或检查 profile 文件：

```powershell
# 创建默认 profile（如果存在则不创建）
if (!(Test-Path -Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}

# 查看创建的 profile 文件位置
$PROFILE
```

输出示例：
```
C:\Users\<Username>\Documents\PowerShell\Microsoft.PowerShell_profile.ps1
```

### 步骤 3：编辑 Profile 文件

用任意编辑器（如 VSCode、Notepad++）打开上述文件，添加以下内容：

```powershell
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding
```

### 步骤 4：重启 PowerShell

关闭并重新打开 PowerShell 窗口，使配置生效。

---

## Python UTF-8 配置

### 方法一：环境变量配置（推荐）

这是最简单和最可靠的方法。在 PowerShell 中执行以下命令：

```powershell
# 设置用户级别环境变量（永久有效）
[Environment]::SetEnvironmentVariable("PYTHONIOENCODING", "utf-8", "User")

# 验证设置
[Environment]::GetEnvironmentVariable("PYTHONIOENCODING", "User")
```

或在系统环境变量中手动配置：
1. 右键点击"此电脑" → 属性
2. 点击"高级系统设置"
3. 点击"环境变量"按钮
4. 在"用户变量"中新建变量：
   - 变量名：`PYTHONIOENCODING`
   - 变量值：`utf-8`
5. 点击"确定"保存，**重启 IDE 或命令行工具**

### 方法二：Python 启动脚本配置

编辑 Python 的 `sitecustomize.py` 文件（位于 Python 的 `site-packages` 目录）：

1. 查找 Python 的 site-packages 位置：

```python
import site
print(site.getsitepackages())
```

2. 在该目录中创建或编辑 `sitecustomize.py`，添加以下内容：

```python
import sys
import io

# 设置标准输入、输出、错误流的编码为 UTF-8
sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8')
sys.stderr = io.TextIOWrapper(sys.stderr.buffer, encoding='utf-8')
sys.stdin = io.TextIOWrapper(sys.stdin.buffer, encoding='utf-8')
```

### 方法三：代码级配置

在 Python 脚本开头添加以下代码：

```python
import sys
import io

# 设置标准输出编码为 UTF-8
if sys.stdout.encoding != 'utf-8':
    sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8')
if sys.stderr.encoding != 'utf-8':
    sys.stderr = io.TextIOWrapper(sys.stderr.buffer, encoding='utf-8')
```

---

## 验证配置

### 验证 PowerShell 编码

在 PowerShell 中运行以下命令：

```powershell
chcp
```

**预期输出**：`Active code page: 65001` （65001 即 UTF-8）

### 验证 Python 编码

在 PowerShell 中运行以下命令：

```powershell
python -c "import sys; print(f'stdout encoding: {sys.stdout.encoding}')"
```

**预期输出**：`stdout encoding: utf-8`

或在 Python 交互式环境中检查：

```python
import sys
print(sys.stdout.encoding)  # 应输出 utf-8
```

---

## 常见问题

### Q1：修改后仍显示编码错误？

**解决方案**：
- 确保已重启 PowerShell 和相关应用（VSCode、IDE 等）
- 检查文件本身的编码格式是否为 UTF-8（在编辑器中查看/设置）
- 运行 `chcp` 命令验证编码是否已生效

### Q2：Python 脚本中文仍然乱码？

**解决方案**：
- 优先使用**方法一**（环境变量配置），设置 `PYTHONIOENCODING=utf-8`
- 确保 Python 文件头部声明编码：`# -*- coding: utf-8 -*-`
- 重启 IDE 后再运行脚本

### Q3：如何为特定项目配置 UTF-8？

**VSCode 配置**：在项目根目录下创建 `.vscode/settings.json`：

```json
{
    "files.encoding": "utf8",
    "python.defaultInterpreterPath": "your_python_path",
    "terminal.integrated.env.windows": {
        "PYTHONIOENCODING": "utf-8"
    }
}
```

**PyCharm 配置**：
- File → Settings → Editor → File Encodings
- IDE Encoding 和 Project Encoding 均设为 UTF-8

---

## 参考资源

- [PowerShell 官方文档](https://learn.microsoft.com/en-us/powershell/)
- [Python 编码官方文档](https://docs.python.org/3/howto/unicode.html)
- [Windows chcp 命令](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/chcp)
