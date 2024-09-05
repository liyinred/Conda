Tmux（Terminal Multiplexer）是一个功能强大的终端复用工具，允许用户在单个终端窗口内运行多个独立的终端会话。Tmux 的基本概念包括窗口（window）、面板（pane）和会话（session）。下面是一些常用的 Tmux 命令，帮助你更好地管理你的终端会话。

### Tmux 基础命令

以下是 Tmux 的一些基础命令：

1. **启动 Tmux**
    
    ```bash
    tmux
    ```
    
    这个命令会启动一个新的 Tmux 会话。
    
2. **新建会话（New session）**
    
    ```bash
    tmux new -s session_name
    ```
    
    使用 `-s` 参数可以给会话命名，例如 `session_name`，方便后续管理。
    
3. **列出所有会话（List sessions）**
    
    ```bash
    tmux ls
    ```
    
    显示当前所有的 Tmux 会话。
    
4. **连接到现有会话（Attach to session）**
    
    ```bash
    tmux attach -t session_name
    ```
    
    连接到指定的会话 `session_name`。
    
5. **关闭会话（Kill session）**
    
    ```bash
    tmux kill-session -t session_name
    ```
    
    关闭指定的会话。
    
6. **分离会话（Detach from session）**
    
    ```bash
    tmux detach
    ```
    
    使用快捷键 `Ctrl + b`，然后按 `d`，可以将当前会话分离，但不关闭会话。
    

### Tmux 窗口管理

窗口是 Tmux 中的基本工作单元，每个窗口都可以包含多个面板。

1. **新建窗口（New window）**
    
    ```bash
    tmux new-window -n window_name
    ```
    
    使用 `-n` 参数可以为新窗口命名。
    
2. **切换窗口（Switch window）**
    
    ```bash
    tmux select-window -t window_index
    ```
    
    `window_index` 是窗口的编号，通常从 0 开始。
    
3. **重命名窗口（Rename window）**
    
    ```bash
    tmux rename-window window_name
    ```
    
    将当前窗口重命名为 `window_name`。
    
4. **关闭窗口（Kill window）**
    
    ```bash
    tmux kill-window -t window_index
    ```
    
    关闭指定的窗口。
    

### Tmux 面板管理

面板允许你在同一个窗口中将屏幕划分为多个区域，并在每个区域中运行不同的命令。

1. **垂直分割面板（Vertical split pane）**
    
    ```bash
    tmux split-window -v
    ```
    
    垂直分割当前窗口，将新面板放置在下面。
    
2. **水平分割面板（Horizontal split pane）**
    
    ```bash
    tmux split-window -h
    ```
    
    水平分割当前窗口，将新面板放置在右侧。
    
3. **切换面板（Switch pane）**
    
    ```bash
    tmux select-pane -t pane_index
    ```
    
    切换到指定的面板，面板编号从 0 开始。
    
4. **调整面板大小（Resize pane）**
    
    ```bash
    tmux resize-pane -D 10  # 向下增加 10 行
    tmux resize-pane -U 10  # 向上减少 10 行
    tmux resize-pane -R 10  # 向右增加 10 列
    tmux resize-pane -L 10  # 向左减少 10 列
    ```
    

### Tmux 会话管理快捷键

Tmux 有一些常用的快捷键，使用起来非常高效。在 Tmux 中，所有的快捷键都需要先按 `Ctrl + b`，然后再按其他键。

1. **分离会话**
    
    ```bash
    Ctrl + b, d
    ```
    
    分离当前会话。
    
2. **切换面板**
    
    ```bash
    Ctrl + b, o
    ```
    
    切换到下一个面板。
    
3. **关闭面板**
    
    ```bash
    Ctrl + b, x
    ```
    
    关闭当前面板。
    
4. **重命名窗口**
    
    ```bash
    Ctrl + b, ,
    ```
    
    重命名当前窗口。
    
5. **上下左右切换面板**
    
    ```bash
    Ctrl + b, [方向键]
    ```
    
    使用方向键切换面板，例如 `Ctrl + b, 左箭头` 切换到左侧面板。
    

### 自定义 Tmux 配置

你可以通过配置文件 `~/.tmux.conf` 自定义 Tmux 行为。例如，可以设置更方便的快捷键或改变面板颜色。以下是一个简单的配置示例：

```bash
# 设置前缀为 Ctrl + a
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# 设置分割面板的快捷键
bind | split-window -h  # 垂直分割
bind - split-window -v  # 水平分割

# 允许鼠标选择窗口和面板
set -g mouse on
```
