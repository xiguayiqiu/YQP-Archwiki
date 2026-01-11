**[返回文件目录](File_Directory.md)**
# Waybar 配置指南

## 目录

- [Waybar 简介](#waybar-简介)
- [快速入门](#快速入门)
- [安装指南](#安装指南)
- [配置文件结构](#配置文件结构)
- [核心模块配置](#核心模块配置)
- [样式自定义](#样式自定义)
- [高级功能](#高级功能)
- [与 Arch Linux 集成](#与-arch-linux-集成)
- [常见问题与解决方案](#常见问题与解决方案)
- [实用配置示例](#实用配置示例)
- [术语表](#术语表)

---

## Waybar 简介

Waybar 是一款现代化、高性能的 Wayland 状态栏程序，专为 wlroots 基础的合成器（如 Sway、Hyprland）设计。作为 i3status/i3blocks 的替代品，Waybar 提供了丰富的模块支持、灵活的 CSS 样式定制以及多显示器支持。

### 主要特性

| 特性 | 描述 |
|------|------|
| **模块化设计** | 支持丰富的内置模块和自定义脚本 |
| **CSS 样式** | 完全自定义外观，支持动画和过渡效果 |
| **多托盘支持** | 支持多个系统托盘区域 |
| **多显示器** | 自动检测和配置多个显示器 |
| **弹出菜单** | 支持交互式弹出菜单 |
| **JSON 配置** | 使用 JSON 格式配置，易于理解和维护 |

---

## 快速入门

### 什么是 Waybar？

Waybar 是一个为 Wayland 合成器设计的状态栏，它允许你在屏幕上显示各种系统信息，如时间、网络状态、电池电量等。与传统的 X11 状态栏不同，Waybar 充分利用了 Wayland 协议的优势，提供更好的性能和兼容性。

### 为什么选择 Waybar？

- **高度可定制**：通过 JSON 配置文件和 CSS 样式表，可以完全自定义外观和行为
- **轻量级**：占用资源少，运行流畅
- **模块化**：支持多种内置模块和自定义脚本
- **Wayland 原生**：专为 Wayland 设计，支持最新的 Wayland 特性
- **活跃开发**：持续更新，支持最新的 Wayland 合成器

### 快速开始

对于 Arch Linux 用户，只需几步即可开始使用 Waybar：

1. **安装 Waybar**：
   ```bash
   sudo pacman -S waybar
   ```

2. **创建基本配置文件**：
   ```bash
   mkdir -p ~/.config/waybar
   cp /etc/xdg/waybar/config ~/.config/waybar/
   cp /etc/xdg/waybar/style.css ~/.config/waybar/
   ```

3. **启动 Waybar**：
   ```bash
   waybar &
   ```

4. **自动启动（针对 Sway 用户）**：
   在 `~/.config/sway/config` 中添加：
   ```bash
   exec waybar
   ```

### 验证安装

运行以下命令检查 Waybar 是否正常工作：
```bash
waybar --version
```

如果一切正常，你将看到 Waybar 的版本信息，并且状态栏会显示在屏幕上。

---

## 安装指南

### 系统要求

对于 Arch Linux 用户，Waybar 需要以下依赖：

- **Wayland 合成器**：如 Sway、Hyprland、Wayfire 等（支持 wlr-layer-shell-unstable-v1 协议）
- **GTK+ 3.0**：用于托盘图标支持
- **JSON-GLib**：用于配置文件解析
- **libdbusmenu-gtk3**：用于系统托盘支持
- **libnl**：用于网络模块
- **libpulse**：用于 PulseAudio 模块

### Arch Linux 安装方法

#### 标准安装（推荐）

使用 Arch Linux 官方仓库安装稳定版本：

```bash
sudo pacman -S waybar
```

#### 安装开发版本

从 AUR 安装最新的开发版本：

```bash
# 使用 yay
yay -S waybar-git

# 或使用 paru
paru -S waybar-git
```

#### 从源码编译

对于高级用户，可以从源码编译 Waybar：

```bash
# 安装编译依赖
sudo pacman -S base-devel cmake meson git \
    wayland wayland-protocols wlroots \
    gtk3 libdbusmenu-gtk3 json-glib libpugixml \
    libsigc++ libudev libinput libxkbcommon \
    libnl libpulse

# 克隆仓库
git clone https://github.com/Alexays/Waybar.git
cd Waybar

# 编译安装
meson setup build
ninja -C build
sudo ninja -C build install
```

### 验证安装

```bash
# 检查 Waybar 版本
waybar --version

# 查看可用选项
waybar --help

# 查看支持的模块
waybar --modules
```

### 安装推荐工具

为了充分利用 Waybar 的功能，建议安装以下工具：

```bash
# 用于网络管理
sudo pacman -S networkmanager

# 用于音频控制
sudo pacman -S pavucontrol pamixer

# 用于电池状态
sudo pacman -S acpi

# 用于温度监控
sudo pacman -S lm_sensors

# 用于系统监控
sudo pacman -S htop

# 用于 Nerd Fonts（图标支持）
sudo pacman -S ttf-jetbrains-mono-nerd
```

---

## 配置文件结构

### 配置文件位置

Waybar 使用两个主要配置文件来定义其行为和外观：

| 文件 | 位置 | 作用 |
|------|------|------|
| **配置文件** | `~/.config/waybar/config` | 定义模块、布局和行为 |
| **样式文件** | `~/.config/waybar/style.css` | 定义外观、颜色和动画 |

### 默认配置文件

Arch Linux 提供了默认的配置文件，位于 `/etc/xdg/waybar/` 目录下。你可以将这些文件复制到你的用户目录作为起点：

```bash
mkdir -p ~/.config/waybar
cp /etc/xdg/waybar/config ~/.config/waybar/
cp /etc/xdg/waybar/style.css ~/.config/waybar/
```

### 目录结构

推荐的 Waybar 配置目录结构：

```
~/.config/waybar/
├── config          # 主配置文件
├── style.css       # 主样式表
├── scripts/        # 自定义脚本模块
│   ├── weather.sh  # 天气脚本示例
│   └── updates.sh  # 系统更新检查脚本
└── modules/        # 其他模块相关文件
    └── pywal/      # Pywal 颜色主题缓存
```

### 配置文件语法

Waybar 配置文件使用 JSON 格式，这是一种轻量级的数据交换格式，易于人类阅读和编写。JSON 文件由键值对组成，使用大括号 `{}` 包裹。

#### 基础配置结构

```json
{
    "layer": "top",              // 状态栏层级（top/bottom/overlay）
    "position": "bottom",        // 状态栏位置（top/bottom/left/right）
    "height": 30,                 // 状态栏高度（像素）
    "width": "100%",             // 状态栏宽度（像素或百分比）
    "spacing": 4,                 // 模块间间距（像素）
    "margin": 5,                  // 外边距（像素）
    "modules-left": ["sway/workspaces", "sway/mode"],  // 左侧模块
    "modules-center": ["clock"],                        // 中间模块
    "modules-right": ["network", "pulseaudio", "battery", "tray"]  // 右侧模块
}
```

#### 验证配置文件

在应用配置之前，建议验证 JSON 语法是否正确：

```bash
# 使用 Python 验证
python -m json.tool ~/.config/waybar/config > /dev/null && echo "配置文件语法正确" || echo "配置文件语法错误"

# 或使用 jq 工具（需要安装）
sudo pacman -S jq
jq . ~/.config/waybar/config > /dev/null && echo "配置文件语法正确" || echo "配置文件语法错误"
```

### 全局配置选项详解

| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| `layer` | string | "bottom" | 决定状态栏是显示在窗口前（`top`）还是后面（`bottom`）。可选值：`top`、`bottom`、`overlay` |
| `output` | string/array | - | 指定该状态栏将在哪个屏幕上显示。可以是单个输出名称或输出名称数组 |
| `position` | string | "top" | 状态栏位置，可以是：`top`、`bottom`、`left`、`right` |
| `height` | integer | - | 状态栏使用的高度，留空表示动态值 |
| `width` | integer/string | "100%" | 状态栏使用的宽度，留空表示动态值 |
| `modules-left` | array | [] | 显示在左侧的模块列表 |
| `modules-center` | array | [] | 显示在中央的模块列表 |
| `modules-right` | array | [] | 显示在右侧的模块列表 |
| `margin` | string | - | 边距值，使用不含单位的CSS格式 |
| `margin-top` | integer | - | 顶部边距，不计单位 |
| `margin-left` | integer | - | 左侧边距，不计单位 |
| `margin-bottom` | integer | - | 底部边距，不计单位 |
| `margin-right` | integer | - | 右侧边距，不计单位 |
| `spacing` | integer | 4 | 不同模块之间间隙的大小 |
| `name` | string | - | 可选名称，添加为CSS类，用于样式化多个状态栏 |
| `mode` | string | - | 选择预配置的显示模式之一，支持的值：`dock`、`hide`、`invisible`、`overlay`。注意：此选项覆盖了`layer`、`exclusive`和`passthrough`选项 |
| `start_hidden` | boolean | false | 启动时隐藏状态栏的选项 |
| `modifier-reset` | string | "press" | 定义修饰键重置状态栏可见性的时间。`press`：按修饰键时重置可见性；`release`：释放修饰键时，仅在按下时没有其他动作时才重置可见性 |
| `exclusive` | boolean | true | 向合成器请求专属区域。关闭此功能可以让应用程序窗口出现在状态栏下方或顶部。`overlay`图层默认关闭此功能 |
| `fixed-center` | boolean | true | 倾向于将中心模块固定在中央位置。当为false时，中心模块位于左右模块之间的空间中 |
| `passthrough` | boolean | false | 将指针事件传递到状态栏下的窗口。设计用于`top`或`overlay`图层，且不使用专属区域。`overlay`图层默认启用此功能 |
| `ipc` | boolean | false | 订阅Sway IPC状态栏配置和可视化事件，并通过命令控制状态栏。需要通过命令行参数`-b`传递`swaymsg`配置中的`bar_id`值，或通过`id`选项指定 |
| `id` | string | - | 代表Sway IPC的bar_id。如果你需要覆盖命令行参数`-b`传递给特定状态栏实例的值，可以使用这个 |
| `include` | array | [] | 通往额外配置文件的路径。每个文件可以包含一个带有任意状态栏配置选项的单一对象。对于重复选项，优先考虑第一个定义的值 |
| `reload-style` | boolean | false | 是否允许通过SIGHUP信号重新加载样式 |
| `reload_style_on_change` | boolean | false | 如果在样式表文件或任何导入的CSS文件中检测到修改，启用重新加载CSS样式的选项 |
| `on-sigusr1` | string | "toggle" | 接收到SIGUSR1信号时执行的动作。可能的值：`show`、`hide`、`toggle`、`reload`、`noop` |
| `on-sigusr2` | string | "reload" | 接收到SIGUSR2信号时执行的动作。可能的值：`show`、`hide`、`toggle`、`reload`、`noop` |

### 模块配置选项

| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| `on-update` | string | - | 模块更新时执行的命令 |
| `on-click` | string | - | 左键点击模块时执行的命令 |
| `on-click-release` | string | - | 松开模块上的左键时执行的命令 |
| `on-double-click` | string | - | 双击模块时执行的命令 |
| `on-triple-click` | string | - | 三击模块时执行的命令 |
| `on-click-middle` | string | - | 中键点击模块时执行的命令 |
| `on-click-middle-release` | string | - | 松开模块上的中键时执行的命令 |
| `on-double-click-middle` | string | - | 双击模块中键时执行的命令 |
| `on-triple-click-middle` | string | - | 三击中键模块时执行的命令 |
| `on-click-right` | string | - | 右键点击模块时执行的命令 |
| `on-click-right-release` | string | - | 松开模块上的右键时执行的命令 |
| `on-double-click-right` | string | - | 双击模块右键时执行的命令 |
| `on-triple-click-right` | string | - | 三击右键模块时执行的命令 |
| `on-click-backward` | string | - | 用鼠标后退键点击模块时执行的命令 |
| `on-click-backward-release` | string | - | 松开模块上的鼠标后退按钮时执行的命令 |
| `on-double-click-backward` | string | - | 用鼠标后退按钮双击模块时执行的命令 |
| `on-triple-click-backward` | string | - | 用鼠标后退键三击模块时执行的命令 |
| `on-click-forward` | string | - | 用鼠标前进键点击模块时执行的命令 |
| `on-click-forward-release` | string | - | 松开模块上的鼠标前进按钮时执行的命令 |
| `on-double-click-forward` | string | - | 用鼠标前进键双击模块时执行的命令 |
| `on-triple-click-forward` | string | - | 用鼠标前进键三击模块时执行的命令 |
| `on-scroll-up` | string | - | 向上滚动模块时执行的命令 |
| `on-scroll-down` | string | - | 向下滚动模块时执行的命令 |
| `on-scroll-left` | string | - | 向左滚动模块时执行的命令 |
| `on-scroll-right` | string | - | 向右滚动模块时执行的命令 |

> **注意**：建议不要为同一个鼠标按钮设置多个配置。例如：`on-click`、`on-double-click`、`on-triple-click`定义为不同命令。当触发三击时，模块会依次执行`on-click`、`on-double-click`、`on-triple-click`的命令，因为Gdk会提供此类事件。

### 模块位置配置

Waybar 将状态栏分为三个主要区域：左侧、中间和右侧。你可以根据需要在每个区域添加或移除模块。

```json
{
    "modules-left": [
        "sway/workspaces",    // 工作区模块
        "sway/mode",          // 模式显示（如 Sway 的 resize 模式）
        "custom/separator"    // 自定义分隔符
    ],
    "modules-center": [
        "clock"               // 居中显示的时钟
    ],
    "modules-right": [
        "network",            // 网络状态
        "pulseaudio",         // 音频控制
        "battery",            // 电池状态
        "tray"                // 系统托盘
    ]
}
```

### 多显示器配置

Waybar 支持为不同的显示器配置不同的状态栏：

#### 方法 1：使用数组配置

```json
[
    {
        "output": "DP-1",      // 针对特定显示器
        "modules-left": ["sway/workspaces"],
        "modules-center": ["clock"],
        "modules-right": ["network", "battery"]
    },
    {
        "output": "HDMI-A-1",  // 针对另一显示器
        "modules-left": ["sway/workspaces"],
        "modules-center": ["clock"],
        "modules-right": ["pulseaudio", "tray"]
    }
]
```

#### 方法 2：使用条件配置

```json
{
    "layer": "top",
    "height": 40,
    "modules-left": ["sway/workspaces"],
    "modules-center": ["clock"],
    "modules-right": ["network", "pulseaudio", "battery", "tray"],
    "sway": {
        "all-outputs": true    // 在所有显示器上显示相同配置
    }
}
```

### 如何找到你的显示器名称

要获取可用显示器的名称，可以使用以下命令：

```bash
# 对于 Sway 用户
swaymsg -t get_outputs | grep -i name

# 对于 Hyprland 用户
hyprctl monitors | grep -i name

# 通用方法
waybar --list-monitors
```

---

## 核心模块配置

### 可用模块列表

Waybar 支持多种内置模块，以下是主要模块列表：

| 模块名称 | 描述 | 依赖 |
|----------|------|------|
| `sway/workspaces` | Sway 工作区管理 | Sway |
| `sway/mode` | Sway 模式显示 | Sway |
| `sway/window` | Sway 当前窗口标题 | Sway |
| `hyprland/workspaces` | Hyprland 工作区管理 | Hyprland |
| `hyprland/submap` | Hyprland 子映射显示 | Hyprland |
| `hyprland/window` | Hyprland 当前窗口标题 | Hyprland |
| `clock` | 时钟和日期显示 | 无 |
| `network` | 网络连接状态 | libnl |
| `pulseaudio` | PulseAudio 音量控制 | PulseAudio |
| `wireplumber` | PipeWire 音量控制 | WirePlumber |
| `battery` | 电池状态监控 | 无 |
| `cpu` | CPU 使用率监控 | 无 |
| `memory` | 内存使用率监控 | 无 |
| `temperature` | 温度传感器监控 | 无 |
| `disk` | 磁盘使用情况 | 无 |
| `backlight` | 屏幕亮度控制 | 无 |
| `tray` | 系统托盘 | GTK+ 3 |
| `custom/*` | 自定义脚本模块 | 脚本 |

### 1. 工作区模块 (sway/workspaces)

工作区模块显示 Sway/Hyprland 的工作区信息。

#### 基本配置

```json
{
    "sway/workspaces": {
        "format": "{name}",
        "format-icons": {
            "1": "一",
            "2": "二",
            "3": "三",
            "4": "四",
            "5": "五",
            "urgent": "🔴",
            "focused": "●",
            "default": "○"
        },
        "persistent_workspaces": {
            "1": ["DP-1"],
            "2": ["DP-1", "HDMI-A-1"],
            "3": [],
            "4": [],
            "5": []
        },
        "on-click": "workspace 1",
        "on-scroll-up": "workspace prev",
        "on-scroll-down": "workspace next"
    }
}
```

#### 配置选项

| 选项 | 类型 | 描述 |
|------|------|------|
| `format` | string | 工作区显示格式 |
| `format-icons` | object | 工作区状态对应的图标映射 |
| `persistent_workspaces` | object | 持久化工作区配置 |
| `show-empty` | boolean | 是否显示空工作区 |
| `all-outputs` | boolean | 是否在所有输出上显示 |

#### 高级配置示例

```json
{
    "sway/workspaces": {
        "format": "{name}",
        "format-icons": {
            "1": "",
            "2": "",
            "3": "",
            "4": "",
            "5": "",
            "focused": "",
            "unfocused": "",
            "urgent": ""
        },
        "persistent_workspaces": {
            "1": ["DP-1"],
            "2": ["DP-1"],
            "3": ["HDMI-A-1"],
            "4": ["HDMI-A-1"]
        },
        "click-method": "none"
    }
}
```

### 2. 时钟模块 (clock)

时钟模块显示当前时间和日期，支持多个时区和自定义格式。

#### 基本配置

```json
{
    "clock": {
        "format": "{:%H:%M}",
        "format-alt": "{:%Y-%m-%d %a %H:%M}",
        "tooltip": true,
        "tooltip-format": "{:%Y-%m-%d %H:%M:%S}"
    }
}
```

#### 时区配置

```json
{
    "clock": {
        "format": "{:%H:%M}",
        "timezones": [
            "Asia/Shanghai",
            "Europe/London",
            "America/New_York"
        ],
        "actions": {
            "on-click-right": "tz_next",
            "on-click-left": "tz_prev"
        }
    }
}
```

#### 完整配置选项

| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| `format` | string | "{:%H:%M}" | 主时间格式 |
| `format-alt` | string | - | 交替格式（右键点击切换） |
| `timezones` | array | - | 显示的时区列表 |
| `locale` | string | - | 区域设置 |
| `tooltip` | boolean | false | 是否显示提示框 |
| `tooltip-format` | string | - | 提示框格式 |
| `interval` | integer | 1 | 更新间隔（秒） |
| `actions` | object | - | 点击动作配置 |

#### 日期格式参考

```json
{
    "clock": {
        "format": "{:%H:%M}",           // 14:30
        "format": "{:%H:%M:%S}",        // 14:30:45
        "format": "{:%Y-%m-%d}",        // 2024-01-15
        "format": "{:%Y-%m-%d %a}",     // 2024-01-15 周一
        "format": "{:%Y/%m/%d %H:%M}",  // 2024/01/15 14:30
        "format": "{:%B %d, %Y}",       // January 15, 2024
        "format-alt": "{:%-d %b}",      // 15 Jan (交替显示)
        "interval": 1                   // 更新间隔（秒）
    }
}
```

#### 可用占位符

| 占位符 | 描述 |
|--------|------|
| `{:%H:%M}` | 小时:分钟（24小时制） |
| `{:%H:%M:%S}` | 小时:分钟:秒（24小时制） |
| `{:%Y-%m-%d}` | 年-月-日 |
| `{:%A}` | 完整星期名称 |
| `{:%a}` | 缩写星期名称 |
| `{:%B}` | 完整月份名称 |
| `{:%b}` | 缩写月份名称 |
| `{:%d}` | 日期（两位） |
| `{:%-d}` | 日期（无前导零） |
| `{:%Y}` | 年份（四位） |

### 3. 网络模块 (network)

网络模块显示网络连接状态，支持 WiFi 和以太网。

#### 基本配置

```json
{
    "network": {
        "interface": "wlan0",
        "format": "{ifname} {signal}% {ssid}",
        "format-disconnected": "❌",
        "tooltip": true,
        "tooltip-format": "{ipaddr} • {gwaddr}",
        "on-click": "nmcli connection show --active"
    }
}
```

#### 详细配置

```json
{
    "network": {
        "interface": "wlan0",
        "format": "📶 {signal}% {ssid}",
        "format-wifi": "📶 {signal}%",
        "format-ethernet": "🌐 有线连接",
        "format-disconnected": "❌ 离线",
        "format-alt": "↓{bandwidthDownBytes} ↑{bandwidthUpBytes}",
        "tooltip": true,
        "tooltip-format": "SSID: {ssid}\nIP: {ipaddr}\n网关: {gwaddr}",
        "max-length": 50,
        "on-click": "nm-connection-editor",
        "on-click-right": "nmcli device wifi list"
    }
}
```

#### 以太网配置

```json
{
    "network": {
        "interface": "enp0s25",
        "format": "🌐 {ipaddr}",
        "format-disconnected": "❌",
        "tooltip": true
    }
}
```

#### 完整配置选项

| 选项 | 类型 | 描述 |
|------|------|------|
| `interface` | string | 网络接口名称（留空自动检测） |
| `format` | string | 正常连接显示格式 |
| `format-wifi` | string | WiFi 连接显示格式 |
| `format-ethernet` | string | 以太网连接显示格式 |
| `format-disconnected` | string | 断开连接显示格式 |
| `format-alt` | string | 交替格式（右键点击切换） |
| `tooltip-format` | string | 提示框格式 |
| `max-length` | integer | SSID 最大长度 |
| `interval` | integer | 更新间隔（秒） |
| `on-click` | string | 左键点击执行的命令 |
| `on-click-right` | string | 右键点击执行的命令 |
| `on-scroll-up` | string | 向上滚动执行的命令 |
| `on-scroll-down` | string | 向下滚动执行的命令 |

#### 可用占位符

| 占位符 | 描述 |
|--------|------|
| `{ifname}` | 网络接口名称 |
| `{signal}` | WiFi 信号强度（百分比） |
| `{ssid}` | WiFi 网络名称 |
| `{ipaddr}` | IP 地址 |
| `{gwaddr}` | 网关地址 |
| `{bandwidthDown}` | 下载带宽（B/s） |
| `{bandwidthDownBytes}` | 下载带宽（带单位） |
| `{bandwidthUp}` | 上传带宽（B/s） |
| `{bandwidthUpBytes}` | 上传带宽（带单位） |

### 4. 音频模块 (pulseaudio/wireplumber)

音频模块显示和控制音频设备，支持 PulseAudio 和 WirePlumber (PipeWire)。

#### PulseAudio 基本配置

```json
{
    "pulseaudio": {
        "format": "🔊 {volume}%",
        "format-muted": "🔇 {volume}%",
        "tooltip": true,
        "on-click": "pamixer -t"
    }
}
```

#### PulseAudio 详细配置

```json
{
    "pulseaudio": {
        "format": "{icon} {volume}%",
        "format-bluetooth": "{icon} {volume}%",
        "format-muted": "🔇 {volume}%",
        "format-icons": {
            "headphone": "🎧",
            "hands-free": "📞",
            "headset": "🎧",
            "phone": "📱",
            "portable": "📱",
            "car": "🚗",
            "default": ["🔈", "🔉", "🔊"]
        },
        "tooltip": true,
        "tooltip-format": "{desc}",
        "scroll-step": 5,
        "on-click": "pavucontrol",
        "on-click-right": "pamixer -t"
    }
}
```

#### WirePlumber 配置

```json
{
    "wireplumber": {
        "format": "🎵 {volume}%",
        "format-muted": "🔇",
        "tooltip": true,
        "scroll-step": 5
    }
}
```

#### 完整配置选项

| 选项 | 类型 | 描述 |
|------|------|------|
| `format` | string | 主音量显示格式 |
| `format-muted` | string | 静音时显示格式 |
| `format-bluetooth` | string | 蓝牙设备显示格式 |
| `format-icons` | object/array | 音量图标映射 |
| `tooltip` | boolean | 是否显示提示框 |
| `tooltip-format` | string | 提示框格式 |
| `scroll-step` | integer | 滚动调整音量的步长 |
| `on-click` | string | 左键点击执行的命令 |
| `on-click-right` | string | 右键点击执行的命令 |
| `on-scroll-up` | string | 向上滚动执行的命令 |
| `on-scroll-down` | string | 向下滚动执行的命令 |
| `device` | string | 指定音频设备名称 |
| `sink` | string | 指定音频输出设备 |
| `source` | string | 指定音频输入设备 |
| `interval` | integer | 更新间隔（秒） |

#### 可用占位符

| 占位符 | 描述 |
|--------|------|
| `{volume}` | 音量百分比 |
| `{icon}` | 根据音量和设备类型显示的图标 |
| `{desc}` | 设备描述 |
| `{name}` | 设备名称 |
| `{port}` | 音频端口 |
| `{format}` | 音频格式 |

### 5. 电池模块 (battery)

电池模块显示电池状态，支持单电池和双电池配置。

#### 基本配置

```json
{
    "battery": {
        "bat": "BAT0",
        "adapter": "ADP1",
        "format": "{capacity}% {status}",
        "format-icons": ["", "", "", "", ""],
        "tooltip": true
    }
}
```

#### 详细配置

```json
{
    "battery": {
        "bat": "BAT0",
        "adapter": "ADP1",
        "format": "{icon} {capacity}%",
        "format-charging": "⚡ {capacity}%",
        "format-full": " 100%",
        "format-icons": {
            "10": "",
            "20": "",
            "30": "",
            "40": "",
            "50": "",
            "60": "",
            "70": "",
            "80": "",
            "90": "",
            "100": ""
        },
        "format-time": "{h}时{m}分",
        "tooltip": true,
        "tooltip-format": "{time} 剩余",
        "on-click": "light -S 50"
    }
}
```

#### 双电池配置

```json
{
    "battery": {
        "bat": "BAT0",
        "bat2": "BAT1",
        "adapter": "ADP1",
        "format": "🔋 {capacity}%",
        "format-charging": "⚡ {capacity}%",
        "tooltip": true
    }
}
```

#### 完整配置选项

| 选项 | 类型 | 描述 |
|------|------|------|
| `bat` | string | 主电池名称（如 BAT0） |
| `bat2` | string | 第二个电池名称（如 BAT1） |
| `adapter` | string | 适配器名称（如 ADP1） |
| `format` | string | 主显示格式 |
| `format-charging` | string | 充电时显示格式 |
| `format-discharging` | string | 放电时显示格式 |
| `format-full` | string | 电池充满时显示格式 |
| `format-low` | string | 电池电量低时显示格式 |
| `format-icons` | array/object | 电池图标映射 |
| `format-time` | string | 剩余时间显示格式 |
| `tooltip` | boolean | 是否显示提示框 |
| `tooltip-format` | string | 提示框格式 |
| `low-threshold` | integer | 低电量阈值（百分比） |
| `critical-threshold` | integer | 临界电量阈值（百分比） |
| `time-format` | string | 时间格式 |
| `interval` | integer | 更新间隔（秒） |
| `on-click` | string | 左键点击执行的命令 |
| `on-click-right` | string | 右键点击执行的命令 |
| `on-scroll-up` | string | 向上滚动执行的命令 |
| `on-scroll-down` | string | 向下滚动执行的命令 |

#### 可用占位符

| 占位符 | 描述 |
|--------|------|
| `{capacity}` | 电池容量百分比 |
| `{status}` | 电池状态（Charging/Discharging/Full） |
| `{icon}` | 根据电池容量显示的图标 |
| `{time}` | 剩余时间 |
| `{percentage}` | 电池百分比 |
| `{consumption}` | 电池消耗率（W） |
| `{voltage}` | 电池电压（V） |

### 系统托盘模块 (tray)

系统托盘显示应用程序图标。

#### 基本配置

```json
{
    "tray": {
        "icon-size": 21,
        "spacing": 10
    }
}
```

#### 详细配置

```json
{
    "tray": {
        "icon-size": 20,
        "spacing": 8,
        "show-passive-items": true
    }
}
```

### CPU 模块

```json
{
    "cpu": {
        "format": "CPU {usage}%",
        "format-alt": "CPU {avg} avg",
        "tooltip": true,
        "tooltip-format": "CPU 使用率: {usage}%\n平均负载: {avg}",
        "on-click": "alacritty -e htop",  // 使用 Arch 常用终端和监控工具
        "interval": 10
    }
}
```

### 内存模块

```json
{
    "memory": {
        "format": "RAM {percentage}%",
        "tooltip": true,
        "tooltip-format": "已用: {used}GB\n可用: {available}GB\n总计: {total}GB",
        "on-click": "alacritty -e htop",
        "interval": 10
    }
}
```

### 温度模块

```json
{
    "temperature": {
        "thermal-zone": 0,
        "hwmon-path": "/sys/devices/virtual/thermal/thermal_zone0/temp",
        "format": "🌡️ {temperatureC}°C",
        "critical-threshold": 80,
        "format-critical": "🔥 {temperatureC}°C",
        "tooltip": true,
        "tooltip-format": "CPU 温度: {temperatureC}°C",
        "interval": 15
    }
}
```

### 磁盘使用模块

```json
{
    "disk": {
        "path": "/",
        "format": "💾 {percentage_used}%",
        "tooltip": true,
        "tooltip-format": "已用: {used}\n可用: {free}\n总计: {total}",
        "on-click": "alacritty -e df -h",
        "interval": 30
    }
}
```

### 背光模块

```json
{
    "backlight": {
        "device": "intel_backlight",  // 对于 Intel 显卡
        // "device": "amdgpu_bl0",     // 对于 AMD 显卡
        // "device": "nvidia_backlight",  // 对于 NVIDIA 显卡
        "format": "☀️ {percent}%",
        "scroll-step": 5,
        "on-scroll-up": "brightnessctl set +5%",
        "on-scroll-down": "brightnessctl set 5-%"
    }
}
```

### 更新检查模块（Arch Linux 特定）

```json
{
    "custom/updates": {
        "exec": "checkupdates | wc -l",
        "format": "📦 {}",
        "tooltip": "可用更新: {}",
        "on-click": "alacritty -e sudo pacman -Syu",
        "interval": 3600,  // 每小时检查一次
        "return-type": "text"
    }
}
```

### 系统托盘模块 (tray)

```json
{
    "tray": {
        "icon-size": 20,
        "spacing": 8,
        "show-passive-items": true,
        "show-dnd-items": true,
        "show-legacy-items": true
    }
}
```

### 自定义脚本模块示例

```json
{
    "custom/weather": {
        "exec": "~/.config/waybar/scripts/weather.sh",
        "format": "🌤️ {}",
        "tooltip": true,
        "interval": 300,  // 每5分钟更新一次
        "return-type": "text"
    }
}
```

配套的天气脚本：

```bash
#!/bin/bash
# ~/.config/waybar/scripts/weather.sh

# 安装依赖：sudo pacman -S curl jq

# 使用 wttr.in API 获取天气信息
weather=$(curl -s "wttr.in/Beijing?format=1")
echo "$weather"
```

---

## 样式自定义

### CSS 基础教程（从零开始）

CSS（层叠样式表）是一种用于描述 HTML 或 XML 文档样式的语言，Waybar 使用 CSS 来自定义状态栏的外观。本教程将从零开始讲解 CSS 编写规则，帮助您掌握 Waybar 样式定制。

#### 1. CSS 基本语法

CSS 由**选择器**和**声明块**组成：

```css
选择器 {
    属性1: 值1;
    属性2: 值2;
    /* 注释 */
}
```

- **选择器**：指定要应用样式的元素
- **声明块**：包含一个或多个样式声明，用大括号 `{}` 包裹
- **样式声明**：由属性和值组成，用冒号 `:` 分隔，每个声明以分号 `;` 结束
- **注释**：用 `/* */` 包裹，用于添加说明，不影响样式

#### 2. CSS 选择器

选择器用于选择要应用样式的元素。Waybar 支持以下主要选择器类型：

##### 2.1 元素选择器

选择特定类型的元素，例如 Waybar 的根元素 `waybar`：

```css
waybar {
    background-color: #282828;
    color: #ebdbb2;
}
```

##### 2.2 ID 选择器

使用 `#` 符号选择具有特定 ID 的元素，用于 Waybar 中的模块：

```css
#clock {
    font-weight: bold;
}

#battery {
    color: #a9b665;
}
```

##### 2.3 类选择器

使用 `.` 符号选择具有特定类的元素，用于 Waybar 中的模块组或状态：

```css
.modules-left {
    padding: 0 10px;
}

.module {
    margin: 0 4px;
}
```

##### 2.4 状态选择器

选择处于特定状态的元素，例如悬停、充电中、低电量等：

```css
#network:hover {
    background-color: rgba(255, 255, 255, 0.1);
}

#battery.charging {
    color: #a9b665;
}

#battery.critical {
    color: #fb4934;
}
```

##### 2.5 组合选择器

将多个选择器组合使用：

```css
/* 选择所有模块 */
#clock, #network, #pulseaudio, #battery {
    padding: 0 8px;
}

/* 选择充电中的电池模块 */
#battery.charging {
    color: #a9b665;
}
```

#### 3. 常用 CSS 属性

以下是 Waybar 样式定制中常用的 CSS 属性：

##### 3.1 文本属性

| 属性 | 描述 | 示例值 |
|------|------|--------|
| `color` | 文本颜色 | `#ebdbb2`, `rgba(235, 219, 178, 1)` |
| `font-family` | 字体系列 | `"JetBrains Mono Nerd Font", sans-serif` |
| `font-size` | 字体大小 | `14px`, `1.2em` |
| `font-weight` | 字体粗细 | `normal`, `bold`, `600` |
| `text-align` | 文本对齐 | `left`, `center`, `right` |

##### 3.2 背景属性

| 属性 | 描述 | 示例值 |
|------|------|--------|
| `background-color` | 背景颜色 | `#282828`, `rgba(40, 40, 40, 0.8)` |
| `background-image` | 背景图片 | `url("background.png")`, `linear-gradient(to right, #282828, #3c3836)` |
| `background-size` | 背景大小 | `cover`, `contain`, `100px 100px` |
| `background-position` | 背景位置 | `center`, `top left` |

##### 3.3 边框和圆角

| 属性 | 描述 | 示例值 |
|------|------|--------|
| `border` | 边框样式 | `1px solid #3c3836` |
| `border-radius` | 圆角半径 | `8px`, `50%` |
| `border-top` | 上边框 | `2px solid #fabd2f` |
| `border-bottom` | 下边框 | `2px solid #fb4934` |

##### 3.4 内边距和外边距

| 属性 | 描述 | 示例值 |
|------|------|--------|
| `padding` | 内边距（内容与边框的距离） | `8px`, `4px 8px` |
| `margin` | 外边距（元素与其他元素的距离） | `8px`, `0 auto` |
| `padding-left` | 左内边距 | `8px` |
| `margin-right` | 右外边距 | `10px` |

##### 3.5 尺寸和定位

| 属性 | 描述 | 示例值 |
|------|------|--------|
| `width` | 宽度 | `100%`, `500px` |
| `height` | 高度 | `30px`, `auto` |
| `min-width` | 最小宽度 | `100px` |
| `max-width` | 最大宽度 | `500px` |

##### 3.6 显示和可见性

| 属性 | 描述 | 示例值 |
|------|------|--------|
| `display` | 显示类型 | `block`, `inline`, `flex`, `none` |
| `visibility` | 可见性 | `visible`, `hidden` |
| `opacity` | 透明度 | `1`, `0.5`, `0` |

##### 3.7 阴影和效果

| 属性 | 描述 | 示例值 |
|------|------|--------|
| `box-shadow` | 盒子阴影 | `0 4px 6px rgba(0, 0, 0, 0.3)` |
| `text-shadow` | 文本阴影 | `1px 1px 2px rgba(0, 0, 0, 0.5)` |
| `transition` | 过渡效果 | `all 0.3s ease` |
| `animation` | 动画 | `blink 1s infinite` |

#### 4. Waybar 特定 CSS

Waybar 有一些特定的 CSS 选择器和属性：

##### 4.1 Waybar 模块选择器

| 选择器 | 描述 |
|--------|------|
| `waybar` | Waybar 根元素 |
| `#workspaces` | 工作区模块 |
| `#clock` | 时钟模块 |
| `#network` | 网络模块 |
| `#pulseaudio` | 音频模块 |
| `#battery` | 电池模块 |
| `#cpu` | CPU 模块 |
| `#memory` | 内存模块 |
| `#temperature` | 温度模块 |
| `#disk` | 磁盘模块 |
| `#backlight` | 背光模块 |
| `#tray` | 系统托盘 |
| `.modules-left` | 左侧模块区域 |
| `.modules-center` | 中间模块区域 |
| `.modules-right` | 右侧模块区域 |

##### 4.2 模块状态类

| 类名 | 描述 | 适用模块 |
|------|------|----------|
| `.focused` | 聚焦状态 | 工作区 |
| `.active` | 活动状态 | 工作区 |
| `.urgent` | 紧急状态 | 工作区 |
| `.charging` | 充电中 | 电池 |
| `.discharging` | 放电中 | 电池 |
| `.full` | 充满电 | 电池 |
| `.low` | 低电量 | 电池 |
| `.critical` | 临界电量 | 电池 |
| `.muted` | 静音状态 | 音频 |
| `.playing` | 播放中 | 媒体 |
| `.paused` | 暂停 | 媒体 |

#### 5. CSS 动画

CSS 动画用于创建动态效果，Waybar 支持 CSS 动画：

```css
/* 定义动画 */
@keyframes blink {
    0%, 100% {
        opacity: 1;
    }
    50% {
        opacity: 0.5;
    }
}

/* 应用动画 */
#battery.critical {
    animation: blink 1s infinite;
}
```

#### 6. CSS 注释

使用 `/* */` 添加注释，用于解释代码或暂时禁用样式：

```css
/* 这是一个注释 */
waybar {
    background-color: #282828; /* 背景颜色 */
    /* color: #ffffff; 暂时禁用的样式 */
}
```

#### 7. 如何组织 CSS 代码

1. **按功能分组**：将相关样式放在一起（例如：全局样式、模块样式、状态样式）
2. **使用注释**：添加注释说明样式的用途
3. **保持一致性**：使用一致的缩进和命名约定
4. **使用变量**：在支持 CSS 变量的环境中，使用变量管理颜色和尺寸

#### 8. 调试 CSS

如果样式不生效，可以尝试以下方法调试：

1. **检查语法**：确保所有括号、冒号和分号都正确
2. **重启 Waybar**：修改样式后，需要重启 Waybar 或发送 SIGHUP 信号重新加载
3. **使用简单样式**：从简单样式开始，逐步添加复杂样式
4. **检查选择器**：确保选择器与 Waybar 元素匹配
5. **使用浏览器开发者工具**：对于 Wayland 合成器，可以使用 `wev` 或 `wlr-inspect` 等工具查看元素

### CSS 基础示例

现在，让我们将上述知识应用到 Waybar 样式中：

```css
/* 全局样式 */
waybar {
    background-color: #282828;
    color: #ebdbb2;
    font-family: "JetBrains Mono Nerd Font";
    font-size: 14px;
    border-radius: 8px;
    padding: 0 12px;
    margin: 8px;
}

/* 模块组样式 */
.modules-left {
    padding: 0 10px;
}

.modules-right {
    padding: 0 10px;
}

/* 单个模块样式 */
#clock {
    font-weight: bold;
    color: #fabd2f;
}

#battery {
    color: #a9b665;
}

#battery.critical {
    color: #fb4934;
    animation: blink 1s infinite;
}

/* 动画定义 */
@keyframes blink {
    50% {
        opacity: 0.5;
    }
}
```

### 选择器类型回顾

```css
/* 整个状态栏 */
waybar {
    /* 样式 */
}

/* 特定模块（通过 ID） */
#clock {
    /* 样式 */
}

/* 特定位置 */
.modules-left {
    /* 样式 */
}

/* 特定类 */
.module {
    /* 样式 */
}

/* 悬停状态 */
#network:hover {
    /* 样式 */
}

/* 特定状态 */
#battery.charging {
    /* 样式 */
}

#battery.critical {
    /* 样式 */
}
```

### Waybar CSS 编写规则详解

Waybar 使用 **GTK CSS 引擎** 来渲染样式，这意味着它支持的 CSS 特性是基于 GTK CSS，而不是完整的 W3C CSS 标准。以下是 Waybar 对 CSS 编写的特殊要求和限制：

#### 1. GTK CSS 与 W3C CSS 的区别

GTK CSS 是 GTK 框架使用的 CSS 变体，与标准网页 CSS 有一些重要区别：

| 特性 | W3C CSS | GTK CSS |
|------|--------|---------|
| 盒模型 | 标准盒模型或 IE 盒模型 | 标准盒模型，不支持 `box-sizing` 属性 |
| 单位 | 支持多种单位（px, em, rem, %, vh, vw 等） | 主要支持 `px` 和 `%`，有限支持 `em` |
| 选择器 | 支持复杂选择器 | 支持基本选择器，部分支持组合选择器 |
| 动画 | 支持复杂动画和过渡 | 支持基本动画，部分支持过渡 |
| 变量 | CSS 变量（--var-name） | 支持 GTK 样式属性，有限支持 CSS 变量 |

#### 2. Waybar 支持的 CSS 特性

##### 2.1 选择器

Waybar 支持以下选择器类型：
- 元素选择器（如 `waybar`）
- ID 选择器（如 `#clock`）
- 类选择器（如 `.modules-left`, `.charging`）
- 状态选择器（如 `:hover`, `:active`）
- 简单组合选择器（如 `#battery.charging`）
- 群组选择器（如 `#clock, #battery`）

##### 2.2 属性

Waybar 支持大多数常用的 CSS 属性，包括：
- 颜色和背景属性（`color`, `background-color`, `background-image`）
- 字体属性（`font-family`, `font-size`, `font-weight`）
- 边框和圆角（`border`, `border-radius`）
- 内边距和外边距（`padding`, `margin`）
- 尺寸属性（`width`, `height`）
- 显示属性（`display`, `visibility`, `opacity`）
- 阴影（`box-shadow`, `text-shadow`）
- 过渡和动画（`transition`, `animation`）

##### 2.3 函数

Waybar 支持以下 CSS 函数：
- 颜色函数：`rgb()`, `rgba()`, `#hex`
- 渐变函数：`linear-gradient()`
- 转换函数：`scale()`, `translate()`, `rotate()`

#### 3. Waybar 不支持的 CSS 特性

##### 3.1 高级选择器

- 后代选择器（如 `waybar #clock`）
- 子选择器（如 `waybar > #clock`）
- 相邻兄弟选择器（如 `#clock + #battery`）
- 通用选择器（如 `*`）
- 属性选择器（如 `[class^="module-"]`）

##### 3.2 高级布局属性

- 弹性盒模型（`display: flex` 和相关属性）
- 网格布局（`display: grid` 和相关属性）
- 定位属性（`position: absolute/fixed`）
- 浮动（`float`）

##### 3.3 高级动画和过渡

- 复杂的 `@keyframes` 动画
- 多步骤过渡
- 过渡延迟（`transition-delay`）

##### 3.4 其他不支持的属性

- `box-sizing`
- `text-overflow`
- `white-space`
- `letter-spacing`
- `word-spacing`
- `text-transform`
- `text-decoration`

#### 4. Waybar 特定的 CSS 扩展

Waybar 添加了一些特定的 CSS 扩展，用于增强样式定制能力：

##### 4.1 模块状态类

Waybar 为模块添加了特定的状态类，用于样式定制：

| 类名 | 描述 | 适用模块 |
|------|------|----------|
| `.focused` | 聚焦的工作区 | 工作区 |
| `.active` | 活动的工作区 | 工作区 |
| `.urgent` | 紧急状态的工作区 | 工作区 |
| `.charging` | 充电中 | 电池 |
| `.discharging` | 放电中 | 电池 |
| `.full` | 充满电 | 电池 |
| `.low` | 低电量 | 电池 |
| `.critical` | 临界电量 | 电池 |
| `.muted` | 静音状态 | 音频 |
| `.playing` | 播放中 | 媒体 |
| `.paused` | 暂停 | 媒体 |

##### 4.2 工具提示样式

Waybar 支持自定义工具提示样式：

```css
tooltip {
    background-color: #282828;
    color: #ebdbb2;
    border-radius: 6px;
    padding: 8px 12px;
    font-size: 12px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

tooltip label {
    color: #ebdbb2;
}
```

##### 4.3 工作区按钮样式

Waybar 支持自定义工作区按钮样式：

```css
#workspaces button {
    padding: 4px 12px;
    margin: 0 2px;
    border-radius: 4px;
    background-color: transparent;
    color: #a0a0a0;
    transition: all 0.2s ease;
}

#workspaces button:hover {
    background-color: rgba(255, 255, 255, 0.1);
}

#workspaces button.active {
    background-color: #3c3836;
    color: #f5f5f5;
}
```

#### 5. Waybar CSS 编写最佳实践

##### 5.1 保持简单

- 使用简单的选择器和属性
- 避免复杂的嵌套和组合
- 优先使用 ID 选择器和类选择器

##### 5.2 兼容性考虑

- 避免使用 Waybar 不支持的 CSS 特性
- 使用 GTK CSS 语法，而不是最新的 W3C CSS 语法
- 测试不同 Waybar 版本的兼容性

##### 5.3 性能优化

- 减少不必要的样式规则
- 避免使用复杂的动画和过渡
- 使用简洁的颜色值（如 #hex 格式）

##### 5.4 组织和维护

- 按功能组织样式规则
- 使用清晰的注释
- 为不同的模块和状态使用一致的命名约定

#### 6. 常见 CSS 问题和解决方案

##### 6.1 样式不生效

**问题**：添加的样式没有效果

**解决方案**：
1. 检查选择器是否正确
2. 确保使用的属性是 Waybar 支持的
3. 重启 Waybar 或发送 SIGHUP 信号重新加载样式
4. 检查 CSS 语法错误

##### 6.2 模块位置问题

**问题**：模块位置不符合预期

**解决方案**：
1. 使用 `padding` 和 `margin` 调整模块间距
2. 避免使用复杂的布局属性
3. 调整模块在配置文件中的顺序

##### 6.3 颜色显示问题

**问题**：颜色显示不符合预期

**解决方案**：
1. 使用 GTK 支持的颜色格式
2. 避免使用透明度过高的颜色
3. 测试不同显示环境下的颜色效果

##### 6.4 动画不流畅

**问题**：CSS 动画不流畅或不工作

**解决方案**：
1. 简化动画效果
2. 增加动画持续时间
3. 避免同时使用多个动画
4. 使用 `opacity` 和 `transform` 属性进行动画，这些属性性能更好

#### 7. Waybar CSS 示例（完整）

```css
/* Waybar 全局样式 */
waybar {
    background-color: rgba(30, 30, 46, 0.9);
    color: #cdd6f4;
    font-family: "JetBrains Mono Nerd Font", sans-serif;
    font-size: 13px;
    border-radius: 8px;
    padding: 4px 12px;
    margin: 8px;
    border: 1px solid rgba(255, 255, 255, 0.1);
}

/* 模块组样式 */
.modules-left,
.modules-center,
.modules-right {
    padding: 0 8px;
}

/* 单个模块通用样式 */
#clock, #network, #pulseaudio, #battery, #cpu, #memory {
    padding: 0 8px;
    margin: 0 4px;
    border-radius: 4px;
    min-width: 40px;
    text-align: center;
}

/* 时钟模块 */
#clock {
    color: #fab387;
    font-weight: bold;
}

/* 网络模块 */
#network {
    color: #89b4fa;
}

#network.disconnected {
    color: #f38ba8;
}

/* 音频模块 */
#pulseaudio {
    color: #cba6f7;
}

#pulseaudio.muted {
    color: #a6adc8;
}

/* 电池模块 */
#battery {
    color: #a6e3a1;
}

#battery.charging {
    color: #f9e2af;
}

#battery.discharging {
    color: #a6e3a1;
}

#battery.low {
    color: #f9e2af;
}

#battery.critical {
    color: #f38ba8;
    animation: blink 1s infinite;
}

/* CPU 和内存模块 */
#cpu {
    color: #89dceb;
}

#memory {
    color: #cba6f7;
}

/* 工具提示样式 */
tooltip {
    background-color: rgba(30, 30, 46, 0.9);
    color: #cdd6f4;
    border-radius: 6px;
    padding: 8px 12px;
    font-size: 12px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

/* 工作区样式 */
#workspaces {
    spacing: 4px;
}

#workspaces button {
    padding: 4px 12px;
    border-radius: 4px;
    color: #6c7086;
    background: transparent;
    border: none;
    transition: all 0.2s ease;
}

#workspaces button:hover {
    background-color: rgba(255, 255, 255, 0.1);
}

#workspaces button.active {
    color: #cdd6f4;
    background-color: #45475a;
}

#workspaces button.urgent {
    color: #f38ba8;
    background-color: #45475a;
}

/* 动画定义 */
@keyframes blink {
    0%, 100% {
        opacity: 1;
    }
    50% {
        opacity: 0.5;
    }
}

/* 媒体模块 */
#custom-player {
    color: #a6e3a1;
    padding: 0 8px;
}

#custom-player.playing {
    color: #a6e3a1;
}

#custom-player.paused {
    color: #a6adc8;
}
```

### 常用样式属性

```css
waybar {
    /* 背景 */
    background-color: #282828;
    background: linear-gradient(to right, #282828, #3c3836);
    
    /* 文字颜色 */
    color: #ebdbb2;
    
    /* 字体 */
    font-family: "JetBrains Mono Nerd Font", monospace;
    font-size: 14px;
    font-weight: bold;
    
    /* 边框 */
    border: 1px solid #3c3836;
    border-top: none;
    border-left: none;
    border-right: none;
    
    /* 圆角 */
    border-radius: 8px;
    
    /* 内边距 */
    padding: 0 12px;
    
    /* 外边距 */
    margin: 10px;
    
    /* 阴影 */
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
    
    /* 过渡效果 */
    transition: all 0.3s ease;
}

/* 激活状态 */
#workspaces button.active {
    color: #fabd2f;
    background-color: #3c3836;
}

/* 电池低电量状态 */
#battery.critical {
    color: #fb4934;
    background-color: rgba(251, 73, 52, 0.2);
}

/* 充电状态 */
#battery.charging {
    color: #a9b665;
}
```

### 颜色方案

#### 字体配置

Waybar 支持使用 Nerd Fonts，这是一种包含大量图标的字体集，非常适合在状态栏中使用。在 Arch Linux 上，你可以安装多种 Nerd Fonts：

```bash
# 安装 JetBrains Mono Nerd Font
sudo pacman -S ttf-jetbrains-mono-nerd

# 安装其他常用 Nerd Fonts
sudo pacman -S ttf-fira-code-nerd ttf-hack-nerd ttf-meslo-nerd
```

然后在 CSS 中配置字体：

```css
waybar {
    font-family: "JetBrains Mono Nerd Font", "Font Awesome 6 Free", sans-serif;
    font-size: 14px;
}
```

#### Gruvbox 主题

```css
/* 浅色主题 */
waybar {
    background-color: #fbf1c7;
    color: #3c3836;
    font-family: "JetBrains Mono Nerd Font";
    font-size: 14px;
    border-radius: 8px;
    padding: 4px 12px;
    margin: 8px;
}

/* 深色主题 */
waybar {
    background-color: #282828;
    color: #ebdbb2;
    font-family: "JetBrains Mono Nerd Font";
    font-size: 14px;
    border-radius: 8px;
    padding: 4px 12px;
    margin: 8px;
}

#clock {
    color: #fabd2f;
    font-weight: bold;
}

#battery {
    color: #a9b665;
}

#battery.critical {
    color: #fb4934;
    animation: blink 1s infinite;
}

#network {
    color: #458588;
}

#pulseaudio {
    color: #d3869b;
}

#tray {
    background-color: #928374;
    border-radius: 4px;
    padding: 0 4px;
}

@keyframes blink {
    50% { opacity: 0.5; }
}
```

#### Catppuccin 主题

```css
waybar {
    background-color: #1e1e2e;
    color: #cdd6f4;
    font-family: "JetBrains Mono Nerd Font";
    font-size: 13px;
    border-radius: 8px;
    padding: 0 8px;
    margin: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
}

#workspaces button {
    color: #6c7086;
    background: transparent;
    border: none;
    border-radius: 4px;
    padding: 4px 8px;
    transition: all 0.2s ease;
}

#workspaces button:hover {
    background-color: rgba(255, 255, 255, 0.1);
}

#workspaces button.active {
    color: #cdd6f4;
    background-color: #45475a;
}

#workspaces button.urgent {
    color: #f38ba8;
    background-color: #45475a;
}

#clock {
    color: #fab387;
    font-weight: 500;
    padding: 0 10px;
}

#network {
    color: #89b4fa;
    padding: 0 5px;
}

#pulseaudio {
    color: #cba6f7;
    padding: 0 5px;
}

#battery {
    color: #a6e3a1;
    padding: 0 5px;
}

#battery.charging {
    color: #f9e2af;
}

#battery.critical {
    color: #f38ba8;
    animation: blink 1s infinite;
}

#cpu {
    color: #89dceb;
    padding: 0 5px;
}

#memory {
    color: #cba6f7;
    padding: 0 5px;
}

@keyframes blink {
    50% { opacity: 0.5; }
}
```

#### Pywal 集成（动态主题）

Pywal 是一个自动生成颜色主题的工具，它可以从壁纸中提取颜色并应用到整个系统。在 Arch Linux 上安装 Pywal：

```bash
sudo pacman -S python-pywal
```

然后在 Waybar 样式表中添加：

```css
/* 导入 Pywal 生成的颜色主题 */
@import "file:///home/$USER/.cache/wal/colors-waybar.css";

waybar {
    background-color: @background;
    color: @foreground;
    font-family: "JetBrains Mono Nerd Font";
    font-size: 14px;
    border-radius: 8px;
    padding: 4px 12px;
    margin: 8px;
}

#workspaces button.active {
    background-color: @color2;
    color: @background;
}

#clock {
    color: @color3;
}

#network {
    color: @color4;
}

#pulseaudio {
    color: @color5;
}

#battery {
    color: @color6;
}
```

#### 透明效果

```css
waybar {
    background-color: rgba(30, 30, 46, 0.8);  /* 半透明背景 */
    color: #cdd6f4;
    font-family: "JetBrains Mono Nerd Font";
    font-size: 14px;
    border-radius: 8px;
    padding: 4px 12px;
    margin: 8px;
    border: 1px solid rgba(255, 255, 255, 0.1);
}
```

### 动画效果

```css
/* 呼吸灯效果 */
@keyframes breathe {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.6; }
}

#clock {
    animation: breathe 2s ease-in-out infinite;
}

/* 滑动效果 */
@keyframes slide-in {
    from {
        transform: translateY(-10px);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}

#workspaces {
    animation: slide-in 0.3s ease-out;
}

/* 脉冲效果 */
@keyframes pulse {
    0% { transform: scale(1); }
    50% { transform: scale(1.1); }
    100% { transform: scale(1); }
}

#battery.critical {
    animation: pulse 0.5s ease-in-out infinite;
}
```

### 工具条和分隔符样式

```css
/* 工作区按钮 */
#workspaces button {
    padding: 4px 12px;
    margin: 0 2px;
    border-radius: 4px;
    background-color: transparent;
    color: #a0a0a0;
    transition: all 0.2s ease;
}

#workspaces button:hover {
    background-color: rgba(255, 255, 255, 0.1);
}

#workspaces button.active {
    background-color: #3c3836;
    color: #f5f5f5;
}

#workspaces button.urgent {
    color: #fb4934;
}

/* 自定义分隔符 */
label.custom-separator {
    color: #504946;
    font-size: 12px;
    margin: 0 4px;
}

/* 工具提示 */
tooltip {
    background-color: #1e1e1e;
    color: #e4e4e4;
    border-radius: 6px;
    padding: 8px 12px;
    font-size: 12px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

/* 圆角边框 */
window#waybar.top {
    border-radius: 0 0 12px 12px;
}

window#waybar.bottom {
    border-radius: 12px 12px 0 0;
}
```

### 响应式设计

```css
waybar {
    font-size: 14px;
}

@media (max-width: 768px) {
    waybar {
        font-size: 12px;
        height: 24px;
    }
    
    #tray {
        display: none;
    }
    
    #disk {
        display: none;
    }
}

@media (max-width: 480px) {
    #memory {
        display: none;
    }
    
    #temperature {
        display: none;
    }
}
```

---

## 高级功能

### 自定义模块

使用 `custom/*` 创建自定义模块，这是 Waybar 最强大的功能之一，允许你扩展 Waybar 的功能。

#### 脚本式模块

创建可执行脚本：

```bash
#!/bin/bash
# ~/.config/waybar/scripts/weather.sh

while true; do
    # 获取天气信息
    weather=$(curl -s "wttr.in/Shanghai?format=1")
    echo "天气: $weather"
    sleep 300  # 每5分钟更新一次
done
```

配置模块：

```json
{
    "custom/weather": {
        "exec": "~/.config/waybar/scripts/weather.sh",
        "return-type": "text",
        "format": "{}",
        "interval": -1  // 脚本本身包含循环，所以不需要 Waybar 定期执行
    }
}
```

#### 一次性脚本模块

对于不需要持续运行的脚本，可以使用 `interval` 参数让 Waybar 定期执行：

```json
{
    "custom/updates": {
        "exec": "checkupdates | wc -l",
        "format": "📦 {}",
        "interval": 3600,  // 每小时执行一次
        "return-type": "text"
    }
}
```

---

## 与 Arch Linux 集成

### 与 Pacman 集成

#### 系统更新检查

使用 `checkupdates` 命令（来自 `pacman-contrib` 包）检查可用更新：

```bash
# 安装依赖
sudo pacman -S pacman-contrib
```

配置模块：

```json
{
    "custom/updates": {
        "exec": "checkupdates | wc -l",
        "format": "📦 {}",
        "tooltip": "可用更新: {}",
        "on-click": "alacritty -e sudo pacman -Syu",
        "interval": 3600
    }
}
```

#### AUR 更新检查

使用 `yay` 或 `paru` 检查 AUR 包更新：

```json
{
    "custom/aur-updates": {
        "exec": "yay -Qua | wc -l",
        "format": "📦 {}",
        "tooltip": "AUR 可用更新: {}",
        "on-click": "alacritty -e yay -Syu",
        "interval": 3600
    }
}
```

### 与 Systemd 集成

#### 系统服务状态监控

创建脚本监控重要服务：

```bash
#!/bin/bash
# ~/.config/waybar/scripts/services.sh

services=("NetworkManager" "sshd" "docker")
status=""

for service in "${services[@]}"; do
    if systemctl is-active --quiet "$service"; then
        status+="🟢 $service "
    else
        status+="🔴 $service "
    fi
done

echo "$status"
```

配置模块：

```json
{
    "custom/services": {
        "exec": "~/.config/waybar/scripts/services.sh",
        "format": "{}",
        "tooltip": "系统服务状态",
        "on-click": "alacritty -e systemctl status NetworkManager sshd docker",
        "interval": 60
    }
}
```

### 与 Arch 特有工具集成

#### Brightnessctl 集成

使用 `brightnessctl` 控制屏幕亮度：

```bash
# 安装依赖
sudo pacman -S brightnessctl
```

配置背光模块：

```json
{
    "backlight": {
        "device": "intel_backlight",
        "format": "☀️ {percent}%",
        "scroll-step": 5,
        "on-scroll-up": "brightnessctl set +5%",
        "on-scroll-down": "brightnessctl set 5-%"
    }
}
```

#### Playerctl 集成

使用 `playerctl` 控制媒体播放：

```bash
# 安装依赖
sudo pacman -S playerctl
```

创建脚本：

```bash
#!/bin/bash
# ~/.config/waybar/scripts/player.sh

if playerctl status &> /dev/null; then
    artist=$(playerctl metadata artist)
    title=$(playerctl metadata title)
    status=$(playerctl status)
    
    if [ "$status" = "Playing" ]; then
        echo "▶️ $artist - $title"
    elif [ "$status" = "Paused" ]; then
        echo "⏸️ $artist - $title"
    fi
else
    echo ""
fi
```

配置模块：

```json
{
    "custom/player": {
        "exec": "~/.config/waybar/scripts/player.sh",
        "format": "{}",
        "interval": 2,
        "on-click": "playerctl play-pause",
        "on-click-right": "playerctl next",
        "on-click-middle": "playerctl previous"
    }
}
```

### 与其他 Wayland 工具集成

#### Wofi 集成

使用 `wofi` 作为应用启动器：

```bash
# 安装依赖
sudo pacman -S wofi
```

在 Waybar 配置中添加：

```json
{
    "custom/menu": {
        "exec": "echo '🍔'",
        "format": "{}",
        "on-click": "wofi --show drun",
        "tooltip": "打开应用菜单"
    }
}
```

#### Swaylock 集成

使用 `swaylock` 锁定屏幕：

```bash
# 安装依赖
sudo pacman -S swaylock
```

配置模块：

```json
{
    "custom/lock": {
        "exec": "echo '🔒'",
        "format": "{}",
        "on-click": "swaylock -f -c 000000",
        "tooltip": "锁定屏幕"
    }
}
```

---

## 常见问题与解决方案

### 问题 1：Waybar 不显示

**症状**：启动后状态栏空白

**解决方案**：

```bash
# 检查 Waybar 是否运行
ps aux | grep waybar

# 查看错误信息
waybar 2>&1 | head -20

# 检查配置文件语法
cat ~/.config/waybar/config | python3 -m json.tool
```

**常见原因**：
1. JSON 语法错误
2. 模块名称拼写错误
3. Wayland compositor 不支持
4. 权限问题

### 问题 2：模块不更新

**症状**：时钟、网络等信息不更新

**解决方案**：

```json
{
    "clock": {
        "interval": 1  // 缩短更新间隔
    },
    "network": {
        "interval": 5
    }
}
```

检查网络接口名称：

```bash
ip link show
```

修改配置中的接口名称：

```json
{
    "network": {
        "interface": "enp3s0"  // 正确的接口名
    }
}
```

### 问题 3：字体图标不显示

**症状**：显示方块或问号

**解决方案**：

1. 安装 Nerd Font（Arch Linux）：

```bash
sudo pacman -S ttf-jetbrains-mono-nerd
```

2. 配置字体：

```css
waybar {
    font-family: "JetBrains Mono Nerd Font", "Font Awesome 6 Free";
}
```

3. 验证字体是否正确安装：

```bash
fc-list | grep -i jetbrains
```

### 问题 4：多显示器配置不生效

**症状**：第二显示器不显示状态栏

**解决方案**：

创建显示器特定的配置：

```json
[
    {
        "output": "DP-1",
        "modules-left": ["sway/workspaces"],
        "modules-center": ["clock"],
        "modules-right": ["network", "battery"]
    },
    {
        "output": "HDMI-0",
        "modules-left": ["sway/workspaces"],
        "modules-center": ["clock"],
        "modules-right": ["pulseaudio", "tray"]
    }
]
```

获取显示器名称：

```bash
waybar --list-monitors
# 或
swaymsg -t get_outputs  # 对于 Sway 用户
hyprctl monitors       # 对于 Hyprland 用户
```

### 问题 5：电池模块不工作

**症状**：电池信息不显示

**解决方案**：

1. 检查电池路径：

```bash
ls /sys/class/power_supply/
```

2. 更新配置：

```json
{
    "battery": {
        "bat": "BAT0",  // 或 "BAT1"，根据实际情况调整
        "adapter": "AC"  // 或 "ADP1"，根据实际情况调整
    }
}
```

### 问题 6：系统托盘空白

**症状**：托盘区域没有图标

**解决方案**：

确保使用 GTK 托盘的应用程序：

```json
{
    "tray": {
        "icon-size": 21,
        "spacing": 10,
        "show-passive-items": true,
        "show-dnd-items": true,
        "show-legacy-items": true
    }
}
```

某些应用需要手动启动：

```bash
# 在 Sway 配置中
exec mako  # 通知守护进程
```

### 问题 7：CPU 温度不显示

**症状**：温度模块为 0 或不显示

**解决方案**：

查找正确的热区：

```bash
# 列出所有热区
ls /sys/class/thermal/

# 查看温度（单位为 millidegree Celsius）
cat /sys/class/thermal/thermal_zone0/temp

# 或使用 sensors 命令（需要安装 lm_sensors）
sudo pacman -S lm_sensors
sensors
```

配置正确的路径：

```json
{
    "temperature": {
        "thermal-zone": 0,
        "hwmon-path": "/sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0/hwmon/hwmon0/temp1_input"
    }
}
```

### 问题 8：音频模块无声

**症状**：点击音频模块无声

**解决方案**：

1. 检查音频后端：

```json
{
    "pulseaudio": {
        "enabled": true
    }
}
```

2. 安装音量控制工具（Arch Linux）：

```bash
sudo pacman -S pavucontrol pamixer
```

3. 检查 PulseAudio 服务状态：

```bash
systemctl --user status pulseaudio
```

### 问题 9：自定义模块执行失败

**症状**：自定义脚本模块不显示

**解决方案**：

1. 确保脚本可执行：

```bash
chmod +x ~/.config/waybar/scripts/weather.sh
```

2. 检查脚本输出：

```bash
~/.config/waybar/scripts/weather.sh
```

3. 检查脚本中的错误：

```bash
bash -x ~/.config/waybar/scripts/weather.sh
```

4. 使用正确的输出格式：

```bash
# 文本输出
echo "Hello Waybar"

# JSON 输出
echo '{"text": "Hello", "class": "custom"}'
```

### 问题 10：样式不生效

**症状**：CSS 修改后无效果

**解决方案**：

1. 重启 Waybar：

```bash
killall waybar
waybar &
```

2. 使用 SIGHUP 重新加载配置和样式：

```bash
pkill -HUP waybar
```

3. 检查 CSS 语法：

```bash
# 使用 CSS 验证
cat ~/.config/waybar/style.css | python3 -c "
import re, sys
content = sys.stdin.read()
braces = content.count('{') - content.count('}')
print(f'未匹配的括号: {braces}')
"
```

### 问题 11：Waybar 高 CPU 使用率

**症状**：Waybar 占用过多 CPU 资源

**解决方案**：

1. 增加模块的更新间隔：

```json
{
    "cpu": {
        "interval": 10
    },
    "memory": {
        "interval": 10
    },
    "network": {
        "interval": 10
    }
}
```

2. 检查自定义脚本是否有性能问题：

```bash
# 使用 time 命令检查脚本执行时间
time ~/.config/waybar/scripts/weather.sh
```

3. 禁用不必要的模块：

```json
{
    "modules-right": ["network", "pulseaudio", "battery", "tray"]  // 移除不必要的模块
}
```

### 问题 12：Waybar 崩溃

**症状**：Waybar 意外退出

**解决方案**：

1. 检查 Waybar 版本：

```bash
waybar --version
```

2. 如果使用的是稳定版本，尝试安装开发版本：

```bash
yay -S waybar-git
```

3. 查看系统日志：

```bash
sudo journalctl -f | grep waybar
```

4. 简化配置文件，逐步添加模块，找出导致崩溃的模块：

```json
{
    "modules-left": ["sway/workspaces"],
    "modules-center": ["clock"],
    "modules-right": ["tray"]  // 只保留基本模块
}
```

---

## 实用配置示例

### 完整配置示例

#### Sway 桌面配置

```json
{
    "layer": "top",
    "position": "top",
    "height": 36,
    "spacing": 8,
    "modules-left": [
        "sway/workspaces",
        "sway/mode"
    ],
    "modules-center": [
        "clock"
    ],
    "modules-right": [
        "network",
        "pulseaudio",
        "battery",
        "tray"
    ],
    "sway/workspaces": {
        "format": "{name}",
        "format-icons": {
            "1": "一",
            "2": "二",
            "3": "三",
            "4": "四",
            "5": "五",
            "focused": "●",
            "default": "○"
        }
    },
    "clock": {
        "format": "{:%H:%M}",
        "format-alt": "{:%Y-%m-%d %a}"
    },
    "network": {
        "interface": "wlan0",
        "format": "📶 {signal}% {ssid}",
        "format-disconnected": "❌ 离线"
    },
    "pulseaudio": {
        "format": "🔊 {volume}%",
        "format-muted": "🔇"
    },
    "battery": {
        "bat": "BAT0",
        "format": "{icon} {capacity}%",
        "format-charging": "⚡ {capacity}%"
    },
    "tray": {
        "icon-size": 20,
        "spacing": 8
    }
}
```

#### 完整样式表

```css
* {
    border: none;
    border-radius: 0;
    font-family: "JetBrains Mono Nerd Font", "Font Awesome 6 Free", sans-serif;
    font-size: 14px;
    min-height: 0;
}

window#waybar {
    background-color: #1e1e2e;
    color: #cdd6f4;
    border-radius: 8px;
    margin: 8px;
    padding: 4px 8px;
}

#workspaces {
    spacing: 4px;
}

#workspaces button {
    padding: 4px 12px;
    border-radius: 4px;
    color: #6c7086;
    transition: all 0.2s ease;
}

#workspaces button.active {
    color: #cdd6f4;
    background-color: #45475a;
}

#workspaces button.urgent {
    color: #f38ba8;
}

#workspaces button:hover {
    background-color: #313244;
}

#clock {
    color: #fab387;
    font-weight: 600;
    padding: 0 16px;
}

#network {
    color: #89b4fa;
}

#pulseaudio {
    color: #cba6f7;
}

#battery {
    color: #a6e3a1;
}

#battery.charging {
    color: #f9e2af;
}

#battery.critical {
    color: #f38ba8;
    animation: blink 1s infinite;
}

@keyframes blink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.5; }
}

#tray {
    color: #f5e0dc;
}

tooltip {
    background-color: #1e1e2e;
    border-radius: 6px;
    padding: 8px 12px;
}

tooltip label {
    color: #cdd6f4;
}
```

### Hyprland 配置示例

```json
{
    "layer": "top",
    "position": "top",
    "height": 40,
    "modules-left": [
        "hyprland/workspaces",
        "hyprland/submap"
    ],
    "modules-center": [
        "clock"
    ],
    "modules-right": [
        "pulseaudio",
        "network",
        "battery",
        "tray"
    ],
    "hyprland/workspaces": {
        "format": "{name}",
        "persistent_workspaces": {
            "1": [],
            "2": [],
            "3": [],
            "4": [],
            "5": []
        }
    },
    "hyprland/submap": {
        "format": "⎌ {}"
    },
    "clock": {
        "format": "{:%H:%M}",
        "format-alt": "{:%Y-%m-%d %a}"
    }
}
```

### 最小配置示例

```json
{
    "layer": "top",
    "modules-center": ["clock"],
    "modules-right": ["tray"]
}
```

### 多显示器配置示例

```json
[
    {
        "output": "DP-1",
        "layer": "top",
        "modules-left": ["sway/workspaces"],
        "modules-center": ["clock"],
        "modules-right": ["network", "battery"]
    },
    {
        "output": "DP-2",
        "layer": "top",
        "modules-left": ["sway/workspaces"],
        "modules-center": ["clock"],
        "modules-right": ["pulseaudio", "tray"]
    },
    {
        "output": "HDMI-0",
        "layer": "top",
        "modules-center": ["clock"],
        "modules-right": ["tray"]
    }
]
```

---

## 术语表

### 基础术语

| 术语 | 描述 |
|------|------|
| **Wayland** | 新一代显示服务器协议，旨在替代 X11 |
| **Waybar** | 基于 Wayland 协议的状态栏程序 |
| **wlroots** | Wayland 合成器开发库，用于构建 Wayland 合成器 |
| **Sway** | 基于 wlroots 的平铺式 Wayland 合成器，兼容 i3 |
| **Hyprland** | 基于 wlroots 的动态平铺 Wayland 合成器 |
| **JSON** | JavaScript 对象表示法，Waybar 的配置文件格式 |
| **CSS** | 层叠样式表，用于自定义 Waybar 的外观 |
| **Module** | Waybar 中的功能模块，如时钟、网络、电池等 |
| **Tray** | 系统托盘，用于显示应用程序图标 |

### 配置术语

| 术语 | 描述 |
|------|------|
| **layer** | Waybar 状态栏的层级（top/bottom/overlay） |
| **position** | Waybar 状态栏的位置（top/bottom/left/right） |
| **spacing** | 模块之间的间距（像素） |
| **margin** | 状态栏的外边距（像素） |
| **interval** | 模块更新的时间间隔（秒） |
| **tooltip** | 鼠标悬停时显示的提示信息 |
| **format** | 模块的显示格式 |
| **format-icons** | 模块状态对应的图标映射 |
| **return-type** | 自定义模块的输出类型（text/json） |
| **persistent_workspaces** | 持久化工作区配置，在重启后保持工作区状态 |

### Arch Linux 术语

| 术语 | 描述 |
|------|------|
| **Pacman** | Arch Linux 的包管理器 |
| **AUR** | Arch User Repository，用户贡献的软件仓库 |
| **Waybar-git** | Waybar 的开发版本，从 AUR 安装 |
| **Checkupdates** | 用于检查 Pacman 可用更新的工具（来自 pacman-contrib 包） |
| **Yay** | AUR 助手，用于安装 AUR 包 |
| **Paru** | 另一个 AUR 助手，用于安装 AUR 包 |
| **Brightnessctl** | 用于控制屏幕亮度的工具 |
| **Playerctl** | 用于控制媒体播放的工具 |
| **Wofi** | Wayland 下的应用启动器 |
| **Swaylock** | Sway 的屏幕锁定工具 |

---

## 参考资源

### 官方资源

- [Waybar GitHub 仓库](https://github.com/Alexays/Waybar)
- [Waybar Wiki](https://github.com/Alexays/Waybar/wiki/)
- [Waybar Releases](https://github.com/Alexays/Waybar/releases)

### Arch Linux 资源

- [Arch Linux 官方文档](https://wiki.archlinux.org/)
- [Arch Linux 软件包数据库](https://archlinux.org/packages/)
- [Arch User Repository (AUR)](https://aur.archlinux.org/)

### 相关项目

- [Sway](https://github.com/swaywm/sway) - Wayland compositor
- [Hyprland](https://github.com/hyprwm/Hyprland) - Wayland compositor
- [Nerd Fonts](https://www.nerdfonts.com/) - 图标字体
- [Pywal](https://github.com/dylanar/pywal) - 颜色主题生成

### 社区资源

- [r/unixporn](https://reddit.com/r/unixporn) - 桌面美化社区
- [Waybar 讨论](https://github.com/Alexays/Waybar/discussions)
- [Arch Linux 论坛](https://bbs.archlinux.org/)

---

## 附录

### 模块列表

| 模块名 | 描述 | 依赖 |
|--------|------|------|
| `sway/workspaces` | Sway 工作区 | Sway |
| `sway/mode` | Sway 模式 | Sway |
| `sway/window` | Sway 窗口标题 | Sway |
| `hyprland/workspaces` | Hyprland 工作区 | Hyprland |
| `hyprland/submap` | Hyprland 子映射 | Hyprland |
| `clock` | 时钟 | 无 |
| `network` | 网络状态 | libnl |
| `pulseaudio` | PulseAudio 控制 | PulseAudio |
| `wireplumber` | PipeWire 控制 | WirePlumber |
| `battery` | 电池状态 | 无 |
| `cpu` | CPU 使用率 | 无 |
| `memory` | 内存使用率 | 无 |
| `temperature` | 温度传感器 | 无 |
| `disk` | 磁盘使用 | 无 |
| `backlight` | 屏幕背光 | 无 |
| `tray` | 系统托盘 | GTK+ 3 |
| `custom/*` | 自定义模块 | 脚本 |

### JSON 占位符

| 占位符 | 描述 |
|--------|------|
| `{name}` | 工作区名称 |
| `{signal}` | WiFi 信号强度 |
| `{ssid}` | WiFi 网络名称 |
| `{ipaddr}` | IP 地址 |
| `{volume}` | 音量百分比 |
| `{capacity}` | 电池容量 |
| `{status}` | 电池状态 |
| `{temperatureC}` | 摄氏温度 |
| `{percentage_used}` | 磁盘使用百分比 |
| `{usage}` | CPU 使用率 |
| `{avg}` | CPU 平均负载 |

### 按键修饰符

| 修饰符 | 描述 |
|--------|------|
| `on-click` | 左键点击 |
| `on-click-right` | 右键点击 |
| `on-click-middle` | 中键点击 |
| `on-scroll-up` | 向上滚动 |
| `on-scroll-down` | 向下滚动 |
| `on-drag-up` | 向上拖拽 |
| `on-drag-down` | 向下拖拽 |

---

*文档最后更新：2026年1月11日*
