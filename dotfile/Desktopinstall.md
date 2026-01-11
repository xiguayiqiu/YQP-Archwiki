# Arch Linux 桌面环境安装与配置指南（Sway/Hyprland 为主）

## 一、前置说明

### 1.1 文档适用人群

本指南面向**已完成 Arch Linux 基础安装**（已配置 pacman 源、安装 base/base-devel 包组）的用户，从 0 到 1 讲解桌面环境的安装、配置与调优，兼顾新手友好性与高级特性。

### 1.2 核心术语解释

| 术语          | 含义                                                       |
| ----------- | -------------------------------------------------------- |
| Wayland/X11 | Linux 显示协议，Sway/Hyprland 基于 Wayland，i3/Gnome/KDE 可兼容 X11 |
| 显示管理器（DM）   | 负责登录界面与启动桌面，如 GDM（Gnome）、SDDM（KDE/Sway）、LightDM（Xfce）    |
| `~/.config` | 用户级配置目录（优先级高于系统级 `/etc`），桌面配置默认存放于此                      |
| `$mod`      | 自定义修饰键（通常为 Win 键 = Mod4，Alt 键 = Mod1）                    |
| 平铺桌面        | 窗口自动平铺排列（无需手动拖拽），代表：i3/Sway/Hyprland                     |

### 1.3 风险提示
- 修改配置文件前建议备份：`cp 原文件 原文件.bak`
- 配置语法错误可能导致桌面无法启动，可通过 TTY（Ctrl+Alt+F2~F6）回滚配置
- 命令中的用户名 / 设备 ID 需替换为自身实际值（如 `whoami` 查看用户名）
## 二、桌面环境分类
Linux 桌面核心分为三类，Arch 下均可通过 pacman 一键安装：
- **重桌面**（功能全、资源占用高）：`KDE Plasma`、`Gnome`、`Cinnamon`、`Mate`
- **轻量桌面**（轻量化、低资源）：`LXQT`、`LXDE`、`Enlightenment`、`Budgie`、`Xfce4`
- **平铺桌面**（高效、程序员友好）：`i3wm(i3)`、`dwm`、`bspwm`、`Sway`、`Hyprland`、`River`、`niri`
### 主流推荐
- 重桌面：Gnome、KDE Plasma
- 轻量桌面：Xfce4
- 平铺桌面：Sway（i3 继任者）、Hyprland（现代化 Wayland 桌面）
## 三、传统桌面安装（Gnome/KDE/Xfce4）
### 3.1 Gnome
#### 安装命令
```
sudo pacman -S gnome gnome-extra  # gnome-extra 包含常用组件
sudo systemctl enable --now gdm.service  # 启用并启动 GDM 显示管理器
```
#### 关键说明
- GDM 默认适配 Wayland，无需额外配置
- 安装完成重启后，登录界面选择 “Gnome”（而非 “Gnome on Xorg”）即可
### 3.2 KDE Plasma
#### 安装命令
```
sudo pacman -S plasma kde-applications    #kde-applications包含常用组件
sudo systemctl enable --now sddm.service  # 启用 SDDM 显示管理器
```
#### 关键说明
- KDE 支持 Wayland/X11 双协议，登录时可选择 “Plasma (Wayland)”
- 如需安装 KDE 游戏：`sudo pacman -S kde-games-meta`
### 3.3 Xfce4
#### 安装命令
```
sudo pacman -S xfce4 xfce4-goodies
sudo systemctl enable --now lightdm.service  # 启用 LightDM 显示管理器
```
#### 关键说明
- Xfce4 基于 X11，轻量稳定，适合低配设备
- 可选美化：`sudo pacman -S arc-gtk-theme papirus-icon-theme`
## 四、平铺桌面安装与配置（Sway）
### 4.1 Sway 核心说明
- Sway 是 i3 的 Wayland 实现，配置 99% 兼容 i3
- 无需单独安装显示管理器（推荐 SDDM），需手动补全桌面功能组件
### 4.2 安装依赖
```
# 基础组件
sudo pacman -S sway alacritty bemenu waybar mako swaylock swayidle
# 截图/剪贴板
sudo pacman -S grim slurp wl-clipboard clipman
# 音频（Wayland 专属）
sudo pacman -S pipewire wireplumber pipewire-pulse
# 输入法（中文必备）
sudo pacman -S fcitx5 fcitx5-chinese-addons fcitx5-gtk fcitx5-qt
# 美化/工具
sudo pacman -S picom jq neovim htop nm-applet volumeicon cbatticon
# 字体（解决中文/图标显示问题）
sudo pacman -S ttf-noto-sans-mono ttf-noto-sans-cjk-sc ttf-font-awesome
# 显示管理器（可选，推荐 SDDM）
sudo pacman -S sddm
sudo systemctl enable --now sddm.service
```
### 4.3 初始化配置
#### 步骤 1：创建配置目录
```
mkdir -p ~/.config/sway  # 必做，否则配置无法加载
```
#### 步骤 2：复制系统默认配置
```
cp /etc/sway/config ~/.config/sway/
# 修改配置文件权限（替换 yiqiu 为自身用户名，可用 whoami 查看）
chown -R $(whoami):$(whoami) ~/.config/sway
```
### 4.4 Sway 完整配置（新手可直接复制）

```
# ======================== 1. 基础变量定义（核心复用） ========================
# 定义 Mod 键（Win 键=Mod4，Alt 键=Mod1）
set $mod Mod4
set $alt Mod1
# 终端模拟器
set $term alacritty
# 程序启动器
set $menu bemenu-run --fn "Noto Sans Mono-10" --width 60 --height 30
# 截图工具
set $screenshot grim -g "$(slurp)" ~/Pictures/screenshots/$(date +%F_%T).png
# 锁屏命令（美化版 swaylock）
set $lock swaylock \
    --image ~/wallpaper.jpg \
    --scaling fill \
    --color 000000cc \
    --inside-color 00000088 \
    --ring-color 61afef88 \
    --key-hl-color e06c75ff \
    --bs-hl-color 98c379ff \
    --text-color ffffffff \
    --inside-ver-color 98c37988 \
    --ring-ver-color 98c379ff \
    --inside-wrong-color e06c7588 \
    --ring-wrong-color e06c75ff

# ======================== 2. 字体与颜色（高级美化） ========================
# 主字体（支持多字体回退+图标字体）
font pango:Noto Sans Mono 10,FontAwesome 9,Noto Sans CJK SC 10
# 全局颜色方案（适配 Nord 主题）
# 格式：background border text indicator child_border
client.focused        #2e3440 #5e81ac #d8dee9 #5e81ac #5e81ac
client.focused_inactive #2e3440 #3b4252 #d8dee9 #3b4252 #3b4252
client.unfocused      #2e3440 #2e3440 #d8dee9 #2e3440 #2e3440
client.urgent         #2e3440 #bf616a #d8dee9 #bf616a #bf616a
client.placeholder    #2e3440 #2e3440 #d8dee9 #2e3440 #2e3440
client.background     #2e3440

# ======================== 3. 输出（高级多显示器/高分屏配置） ========================
# 全局输出配置
output * {
    # 全局缩放（适配高分屏，根据屏幕 DPI 调整）
    scale 1.0
    # 刷新率（自动匹配最高）
    adaptive_sync on
    # 全局壁纸
    bg ~/wallpaper.jpg fill
}

# 自定义显示器配置（替换为你的硬件名称，可用 swaymsg -t get_outputs 查看）
output HDMI-A-1 {
    resolution 2560x1440
    position 0,0
    scale 1.0
    bg ~/wallpaper_hdmi.jpg fill
}
output DP-1 {
    resolution 3840x2160
    position 2560,0
    scale 2.0  # 4K 屏缩放 2 倍
    adaptive_sync on
}
output eDP-1 {
    # 笔记本内置屏，可 disable 禁用
    # disable
    resolution 1920x1080
    position 0,1440
    scale 1.0
}

# ======================== 4. 输入设备（高级配置） ========================
# 全局输入配置
input * {
    xkb_options caps:escape  # CapsLock 映射为 ESC（程序员友好）
}

# 键盘（多布局+切换+重复速率）
input type:keyboard {
    xkb_layout "us,cn"  # 英文+中文
    xkb_variant ""
    xkb_options "grp:alt_shift_toggle,grp_led:scroll"  # Alt+Shift 切换布局，ScrollLock 指示灯
    repeat_delay 400    # 按键重复延迟
    repeat_rate 50      # 按键重复速率
}

# 鼠标（高级加速度+侧键映射）
input type:mouse {
    accel_profile flat  # 无加速度（游戏/办公友好）
    pointer_accel 0.2   # 微调加速度
    # 侧键映射（示例：前进/后退）
    bindsym --whole-window BTN_EXTRA exec swaymsg focus left
    bindsym --whole-window BTN_SIDE exec swaymsg focus right
}

# 触控板（高级手势）
input type:touchpad {
    tap enabled                 # 点击启用
    tap_button_map lrm          # 轻击分区（左/右/中）
    natural_scroll enabled      # 自然滚动
    scroll_method two_finger    # 双指滚动
    dwt enabled                 # 禁用桌面时触控板
    click_method clickfinger    # 多指点击（2 指右键，3 指中键）
}

# ======================== 5. 窗口管理（高级特性） ========================
# 窗口边框与间隙（美化+空间利用）
default_border pixel 2         # 极简像素边框
default_floating_border normal # 浮动窗口正常边框
gaps inner 10                  # 窗口内间隙
gaps outer 5                   # 屏幕外间隙
gaps smart_gaps on             # 单窗口时自动关闭间隙
gaps smart_borders on          # 相邻窗口自动合并边框

# 窗口布局
workspace_layout tiled         # 默认平铺布局
focus_follows_mouse yes        # 鼠标跟随焦点
mouse_warping output           # 切换工作区时鼠标跳转到对应显示器
resize increment 10            # 调整窗口步长
floating_modifier $mod         # 按住 Mod 拖动浮动窗口

# 高级窗口规则（精准控制）
# 强制特定应用浮动
for_window [class="Firefox" title="Picture-in-Picture"] floating enable
for_window [class="WeChat" "Wine"] floating enable
for_window [class="Gimp" role="gimp-toolbox"] floating enable
# 强制应用全屏
for_window [class="Steam" title="Steam"] fullscreen enable
# 应用启动后自动移动到指定工作区
for_window [class="Firefox"] move to workspace 2: 🌐 浏览器
for_window [class="Code"] move to workspace 3: 📝 编辑器
for_window [class="WeChat"] move to workspace 4: 💬 聊天
# 紧急窗口自动聚焦
for_window [urgent=latest] focus
# 关闭窗口动画
for_window [class=".*"] opacity 1.0

# ======================== 6. 工作区（高级命名+绑定） ========================
# 带图标/中文的工作区命名
set $ws1 "1:  终端"
set $ws2 "2: 🌐 浏览器"
set $ws3 "3: 📝 编辑器"
set $ws4 "4: 💬 聊天"
set $ws5 "5: 🎮 游戏"
set $ws6 "6: 🎵 音乐"
set $ws7 "7: 📊 办公"
set $ws8 "8: 🛠️ 工具"
set $ws9 "9: 🧪 测试"
set $ws10 "10: 📥 下载"

# 工作区绑定到指定显示器
workspace $ws1 output HDMI-A-1
workspace $ws2 output HDMI-A-1
workspace $ws3 output DP-1
workspace $ws4 output DP-1
workspace $ws5 output eDP-1

# ======================== 7. 快捷键（全功能覆盖+高级绑定） ========================
# 基础操作
bindsym $mod+Return exec $term                  # 打开终端
bindsym $mod+q kill                             # 关闭窗口
bindsym $mod+d exec $menu                       # 启动器
bindsym $mod+Escape exec $lock                  # 快速锁屏
bindsym Print exec $screenshot                  # 区域截图
bindsym $mod+Print exec grim ~/Pictures/screenshots/$(date +%F_%T).png  # 全屏截图

# 工作区操作
bindsym $mod+1 workspace $ws1
bindsym $mod+2 workspace $ws2
bindsym $mod+3 workspace $ws3
bindsym $mod+4 workspace $ws4
bindsym $mod+5 workspace $ws5
bindsym $mod+6 workspace $ws6
bindsym $mod+7 workspace $ws7
bindsym $mod+8 workspace $ws8
bindsym $mod+9 workspace $ws9
bindsym $mod+0 workspace $ws10

# 移动窗口到工作区
bindsym $mod+Shift+1 move window to workspace $ws1
bindsym $mod+Shift+2 move window to workspace $ws2
bindsym $mod+Shift+3 move window to workspace $ws3
bindsym $mod+Shift+4 move window to workspace $ws4
bindsym $mod+Shift+5 move window to workspace $ws5
bindsym $mod+Shift+6 move window to workspace $ws6
bindsym $mod+Shift+7 move window to workspace $ws7
bindsym $mod+Shift+8 move window to workspace $ws8
bindsym $mod+Shift+9 move window to workspace $ws9
bindsym $mod+Shift+0 move window to workspace $ws10

# 窗口焦点与移动（Vim 键位）
bindsym $mod+h focus left
bindsym $mod+j focus down
bindsym $mod+k focus up
bindsym $mod+l focus right
bindsym $mod+Shift+h move left 10px
bindsym $mod+Shift+j move down 10px
bindsym $mod+Shift+k move up 10px
bindsym $mod+Shift+l move right 10px

# 窗口大小调整（高级）
bindsym $mod+Ctrl+h resize shrink width 10px
bindsym $mod+Ctrl+j resize grow height 10px
bindsym $mod+Ctrl+k resize shrink height 10px
bindsym $mod+Ctrl+l resize grow width 10px

# 布局切换
bindsym $mod+s layout stacking    # 堆叠
bindsym $mod+w layout tabbed       # 标签页
bindsym $mod+e layout toggle split # 平铺/拆分
bindsym $mod+f fullscreen toggle   # 全屏
bindsym $mod+Shift+space floating toggle  # 浮动/平铺切换

# 系统操作（高级）
bindsym $mod+Shift+e exec swaynag -t warning -m '确定退出 Sway?' -b '退出' 'swaymsg exit'  # 安全退出
bindsym $mod+Shift+r reload        # 重载配置（无需重启）
bindsym $mod+Ctrl+r exec $term -e htop  # 打开系统监控
bindsym $mod+Ctrl+s exec $term -e nvim ~/.config/sway/config  # 快速编辑配置

# Wayland 专属快捷键
bindsym $mod+v exec wl-paste > ~/clipboard.txt  # 粘贴剪贴板到文件
bindsym $mod+Shift+v exec wl-copy < ~/clipboard.txt  # 复制文件到剪贴板
bindsym $mod+Ctrl+v exec clipman menu  # 剪贴板历史菜单

# ======================== 8. 状态栏（Waybar 高级配置） ========================
# 禁用 sway 内置 bar，使用 waybar（功能更全）
bar {
    status_command waybar
}

# ======================== 9. 自动启动（高级常驻程序） ========================
# 输入法（Fcitx5，Wayland 适配）
exec export GTK_IM_MODULE=fcitx
exec export QT_IM_MODULE=fcitx
exec export XMODIFIERS=@im=fcitx
exec export SDL_IM_MODULE=fcitx
exec export GLFW_IM_MODULE=ibus
exec fcitx5 &

# 音频服务（PipeWire）
exec pipewire &
exec wireplumber &
exec pipewire-pulse &

# 通知守护进程
exec mako &

# 剪贴板历史
exec wl-paste --watch clipman store &

# 系统托盘
exec nm-applet --indicator &
exec volumeicon &
exec cbatticon &

# 自动锁屏/休眠（高级电源管理）
exec swayidle -w \
    timeout 300 '$lock' \
    timeout 600 'swaymsg "output * dpms off"' \
    resume 'swaymsg "output * dpms on"' \
    before-sleep '$lock' &

# 透明效果（需要 compositor 支持）
exec picom --config ~/.config/picom.conf &

# 热插拔显示器自动配置
exec sway-output-switcher &
```
### 4.5 输入法额外配置（必做）
创建 `~/.pam_environment` 文件并添加以下内容，解决 Wayland 下输入法不生效问题：

```
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE DEFAULT=fcitx
XMODIFIERS DEFAULT=@im=fcitx
SDL_IM_MODULE DEFAULT=fcitx
```
### 4.6 启动与调试
#### 启动 Sway
- 若使用 SDDM：登录界面选择 “Sway” 即可
- 若手动启动：`exec sway`（需在 TTY 中执行）
#### 调试命令
```
sway -d  # 调试模式启动，查看详细日志
swaymsg -t get_outputs  # 查看显示器信息
swaymsg -t get_inputs   # 查看输入设备信息
```
## 五、平铺桌面安装与配置（Hyprland）
### 5.1 Hyprland 核心说明
- 现代化 Wayland 平铺桌面，配置语法更简洁，动画 / 特效更丰富
- 兼容性：0.52 版本后修复了触控板相关 Bug，推荐安装最新版
### 5.2 安装依赖
```
# 基础组件
sudo pacman -S hyprland alacritty wofi waybar dunst swaylock grim slurp
# 音频/输入法（同 Sway）
sudo pacman -S pipewire wireplumber pipewire-pulse fcitx5 fcitx5-chinese-addons fcitx5-gtk fcitx5-qt
# 美化/工具
sudo pacman -S hyprpaper jq thunar ttf-jetbrains-mono-nerd ttf-noto-sans-cjk-sc
# 显示管理器（推荐 SDDM）
sudo pacman -S sddm
sudo systemctl enable --now sddm.service
```
### 5.3 初始化配置
#### 步骤 1：创建配置目录
```
mkdir -p ~/.config/hypr
```
#### 步骤 2：创建核心配置文件
```
touch ~/.config/hypr/hyprland.conf
```
### 5.4 Hyprland 完整配置（新手可直接复制）
```
# ------------------------------
# 1. 全局变量定义（复用性核心）
# ------------------------------
# 基础修饰键
$mod = SUPER
$alt = ALT
$ctrl = CTRL
$shift = SHIFT

# 常用程序（按需修改）
$terminal = alacritty
$file_manager = thunar
$launcher = wofi --show drun --allow-images
$browser = firefox
$editor = code
$locker = swaylock -f -c 000000
$screenshot = grim -g "$(slurp)" - | wl-copy

# 自定义颜色（统一管理外观）
$color_primary = rgba(66,133,244,1)    # 谷歌蓝（主色）
$color_secondary = rgba(156,39,176,1)  # 紫（辅色）
$color_bg = rgba(30,30,30,1)           # 背景色
$color_fg = rgba(240,240,240,1)        # 前景色
$color_active = rgba(76,175,80,1)      # 激活窗口色
$color_inactive = rgba(100,100,100,1)  # 非激活窗口色

# ------------------------------
# 2. 通用全局设置（基础+高级）
# ------------------------------
# 基础窗口设置
border_size = 2
gaps_in = 8
gaps_out = 12
layout = dwindle  # 默认布局：dwindle（螺旋）/master（主从）

# 高级：窗口行为
allow_tearing = no                    # 禁用撕裂（性能优先可开）
decoration_render_mode = 1            # 装饰渲染模式：0=快速，1=高质量
force_default_wallpaper = 0           # 禁用默认壁纸
focus_on_activation = 1               # 激活窗口时自动聚焦
focus_follows_mouse = 1               # 鼠标跟随焦点（1=跟随，2=点击才聚焦）
mouse_move_enables_dpms = 1           # 鼠标移动唤醒 DPMS（节能）
no_border_on_floating = 0             # 浮动窗口保留边框
rounding = 10                         # 全局圆角（高级：配合装饰模块精细控制）

# 高级：性能优化
max_render_time = 16                  # 最大渲染时间（ms，60 帧=16ms）
vfr = 1                               # 可变帧率（适配高刷显示器）
disable_hyprland_logo = 1             # 禁用启动 logo
disable_splash_rendering = 1          # 禁用启动动画（加快启动）

# ------------------------------
# 3. 显示器高级配置
# ------------------------------
# 基础：自动检测主显示器（1080P=1，2.5K=2，4K=2.5）
monitor=,preferred,auto,1.0

# 高级：多显示器定制（示例：双屏+缩放+旋转）
# monitor=HDMI-A-1,2560x1440@144,0x0,1.0           # 主屏：2K@144Hz
# monitor=DP-1,1920x1080@60,2560x0,1.2,transform,90 # 副屏：1080P@60Hz，缩放1.2，旋转90度
# monitor=eDP-1,1920x1080@60,-1920x0,1.0,mirror,HDMI-A-1 # 笔记本屏镜像主屏

# 高级：显示器特效
monitor_dpms = 1                      # 启用 DPMS 节能
idle_inhibitor = 1                    # 禁止空闲时熄屏（如播放视频）
cursor_zoom = 1.0                     # 光标缩放（适配高分屏）

# ------------------------------
# 4. 输入设备高级配置
# ------------------------------
input {
    # 基础键盘设置
    kb_layout = us,cn
    kb_variant = ,
    kb_options = grp:alt_shift_toggle,grp_led:scroll
    kb_repeat_rate = 30
    kb_repeat_delay = 350

    # 高级：键盘映射（自定义按键）
    kb_bindcode = {
        # 将 CapsLock 映射为 Escape（程序员常用）
        58 = 1
        # 将右 Alt 映射为 Ctrl（进阶按键定制）
        100 = 29
    }

    # 基础鼠标设置
    mouse {
        sensitivity = 0.6
        accel_profile = flat
        left_handed = no
        natural_scroll = no
    }

    # 高级：鼠标高级行为
    mouse_cursor = Bibata-Modern-Classic  # 自定义光标主题
    mouse_move_threshold = 10             # 鼠标移动阈值（防误触）
    scroll_factor = 1.2                   # 滚动速度倍率

    # 基础触控板设置
    touchpad {
        natural_scroll = yes
        tap_to_click = yes
        drag_lock = no
        disable_while_typing = yes
    }

    # 高级：触控板手势（Hyprland v0.38+）
    touchpad {
        # 两指手势
        gesture_two_finger_swipe_left = exec, hyprctl dispatch workspace -1  # 上一工作区
        gesture_two_finger_swipe_right = exec, hyprctl dispatch workspace +1 # 下一工作区
        gesture_two_finger_swipe_up = exec, hyprctl dispatch fullscreen toggle # 最大化
        gesture_two_finger_swipe_down = exec, hyprctl dispatch fakefullscreen toggle # 最小化

        # 三指手势
        gesture_three_finger_click = exec, $terminal     # 打开终端
        gesture_three_finger_swipe_up = exec, $launcher  # 打开启动器
        gesture_three_finger_swipe_down = exec, $locker  # 锁屏

        # 四指手势
        gesture_four_finger_swipe_left = exec, hyprctl dispatch workspace -1
        gesture_four_finger_swipe_right = exec, hyprctl dispatch workspace +1
    }

    # 高级：手写板/数位板（适配 Wacom）
    tablet {
        mode = absolute                   # 绝对定位（数位板）
        sensitivity = 0.8
        pressure_curve = 0,0,1,1          # 压力曲线（线性）
    }
}

# ------------------------------
# 5. 窗口装饰高级特效
# ------------------------------
decoration {
    # 基础装饰
    titlebar = yes
    titlebar_font = JetBrainsMono Nerd Font:size=10:weight=bold
    titlebar_align = center               # 标题栏文字居中
    border_size = 2

    # 高级：模糊特效（性能消耗较高）
    blur = yes
    blur_size = 5
    blur_passes = 3
    blur_ignore_opacity = no              # 不忽略透明窗口
    blur_new_optimizations = yes          # 启用新模糊优化

    # 高级：阴影特效
    shadow = yes
    shadow_range = 15
    shadow_render_power = 5
    shadow_color = rgba(0,0,0,0.6)
    shadow_offset = 5 5                   # 阴影偏移（x,y）
    shadow_ignore_window = true           # 忽略小窗口阴影（性能优化）

    # 高级：圆角与裁剪
    rounding = 12
    clip_shadow = yes                     # 裁剪阴影（避免溢出）
    force_zero_scaling = no               # 禁用缩放时的圆角失真

    # 高级：渐变标题栏（Hyprland v0.39+）
    gradient = yes
    gradient_color = $color_secondary     # 渐变辅色
    gradient_angle = 45                   # 渐变角度
}

# ------------------------------
# 6. 动画高级配置（核心高级特性）
# ------------------------------
animations {
    enabled = yes
    animation_speed = 1.0                 # 动画速度倍率

    # 基础动画
    fade = yes
    fade_duration = 150
    fade_framerate = 60

    # 高级：窗口移动动画
    move = yes
    move_duration = 100
    move_ease = cubic                    # 缓动函数：linear/cubic/quad/etc
    move_smooth = yes                    # 平滑移动

    # 高级：窗口调整大小动画
    resize = yes
    resize_duration = 120
    resize_ease = cubic
    resize_follow_mouse = yes            # 跟随鼠标调整大小

    # 高级：工作区切换动画
    workspace = yes
    workspace_duration = 150
    workspace_ease = cubic
}

# ------------------------------
# 7. 快捷键绑定（核心操作）
# ------------------------------
# ========== 基础操作 ==========
bind = $mod, Return, exec, $terminal  # 打开终端
bind = $mod, E, exec, $file_manager   # 打开文件管理器
bind = $mod, Q, killactive            # 关闭当前窗口
bind = $mod, F, togglefloating        # 切换窗口浮动/平铺
bind = $mod, R, exec, hyprctl reload  # 重载配置

# ========== 工作区 ==========
bind = $mod, 1, workspace, 1          # 切换到工作区1
bind = $mod, 2, workspace, 2          # 切换到工作区2
bind = $mod SHIFT, 1, movetoworkspace, 1  # 移动窗口到工作区1
bind = $mod SHIFT, 2, movetoworkspace, 2  # 移动窗口到工作区2

# ========== 窗口操作 ==========
bindm = $mod, mouse:273, movewindow   # 按住 $mod+鼠标左键移动窗口
bindm = $mod, mouse:274, resizewindow # 按住 $mod+鼠标右键调整窗口大小
bind = $mod, Left, movefocus, l       # 焦点向左移动
bind = $mod, Right, movefocus, r      # 焦点向右移动

# ========== 系统操作 ==========
bind = $mod SHIFT, E, exit,           # 退出 Hyprland
bind = $mod, D, exec, $launcher # 打开应用启动器
bind = $mod SHIFT, L, exec, $locker  # 锁屏
bind = Print, , exec, $screenshot  # 区域截图

# ------------------------------
# 8. 窗口规则（个性化控制）
# ------------------------------
# 基础规则示例
windowrulev2 = float, class:^(firefox)$, title:^(Picture-in-Picture)$  # 画中画浮动
windowrulev2 = size 800 600, class:^(alacritty)$  # 终端固定大小
windowrulev2 = pin, class:^(wechat)$  # 微信置顶
windowrulev2 = fullscreen, class:^(steam)$  # Steam 全屏

# ------------------------------
# 9. 启动自启程序
# ------------------------------
# 必装后台服务
exec-once = hyprpaper          # 壁纸
exec-once = waybar             # 状态栏
exec-once = dunst              # 通知守护进程
exec-once = fcitx5 -d          # 输入法（中文输入必备）

# 环境变量（输入法/应用兼容）
env = GTK_IM_MODULE,fcitx
env = QT_IM_MODULE,fcitx
env = XMODIFIERS,@im=fcitx
env = SDL_IM_MODULE,fcitx

# 触控板禁用（替换设备 ID 为自身值，用 hyprctl devices 查看）
exec-once = hyprctl keyword "device[asuf1204:00-2808:0104-touchpad]" disable
```
### 5.5 关键补充：窗口规则详解
#### 核心语法
```
windowrulev2 = [动作], [匹配条件]
```
#### 匹配条件（常用）

| 匹配类型           | 含义            | 示例                           |
| -------------- | ------------- | ---------------------------- |
| class:^xxx$    | 窗口类名（正则精确匹配）  | class:^(alacritty)$          |
| title:^xxx$    | 窗口标题（正则精确匹配）  | title:^(Picture-in-Picture)$ |
| executable:xxx | 可执行文件名称（精确匹配） | executable:wechat            |

#### 常用动作
```
# 浮动/全屏
windowrulev2 = float, class:^(wechat)$          # 微信浮动
windowrulev2 = fullscreen, class:^(steam)$      # Steam 全屏

# 大小/位置
windowrulev2 = size 400 800, class:^(wechat)$   # 微信固定大小
windowrulev2 = pos 100 100, class:^(wechat)$    # 微信固定位置
windowrulev2 = centerwindow, class:^(calc)$     # 计算器居中

# 工作区
windowrulev2 = workspace 1, class:^(firefox)$   # 浏览器默认在工作区1

# 行为
windowrulev2 = pin, class:^(wechat)$            # 微信置顶
windowrulev2 = noanim, class:^(chrome)$         # Chrome 禁用动画
```
#### 如何获取窗口信息
```
hyprctl clients  # 查看所有窗口的 class/title/executable
```
### 5.6 启动与调试
#### 启动 Hyprland
- SDDM 登录界面选择 “Hyprland” 即可
- 手动启动：`exec Hyprland`（TTY 中执行）
#### 调试命令
```
hyprctl debug  # 查看调试日志
hyprctl devices  # 查看输入设备 ID
hyprctl clients  # 查看窗口信息
hyprctl reload   # 重载配置（修改后无需重启）
```
## 六、通用问题排查
### 6.1 桌面无法启动
- 检查配置语法：`sway -C ~/.config/sway/config -d` 或 `hyprctl check`
- 回滚配置：恢复备份的 `config.bak` 文件
- 切换 TTY（Ctrl+Alt+F2），重新安装依赖：`sudo pacman -S --reinstall sway hyprland`
### 6.2 输入法不生效
- 检查 `~/.pam_environment` 文件是否配置正确
- 重启输入法：`killall fcitx5 && fcitx5 &`
### 6.3 显示器 / 触控板异常
- Sway：`swaymsg input type:touchpad disable`（禁用触控板）
- Hyprland：`hyprctl keyword "device[设备ID]" disable`（替换设备 ID）
- 查看显示器信息：`swaymsg -t get_outputs` / `hyprctl monitors`
## 七、补充说明
1. 所有配置文件路径均为用户级（`~/.config`），修改后无需 sudo 权限
2. 壁纸路径需替换为自身文件（如 `~/Pictures/wallpaper.jpg`）
3. 高分屏缩放需根据实际分辨率调整（1080P=1，2K=1.5，4K=2）
4. 若需卸载桌面：`sudo pacman -Rns sway hyprland` + 禁用显示管理器：`sudo systemctl disable sddm`


