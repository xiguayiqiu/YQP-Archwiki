> 适用场景：UEFI 固件 + GPT 分区表（主流新电脑），双系统（Windows+Arch）或单系统均可
> 
> 核心说明：全程附带风险提示、错误修正、新手注释

**[返回文件目录](File_Directory.md)**
## 一、前期准备（必做）

### 1. 硬件与环境要求

- 8G 以上 U 盘（制作启动介质）
- 可引导的 Windows/Linux 电脑（制作 U 盘）
- 目标电脑：开启 UEFI 模式，关闭安全启动（后续步骤详解）
- 重要：**备份目标电脑所有数据**（分区步骤会清除磁盘数据，双系统需备份 Windows 重要文件）

### 2. 下载 Arch ISO 镜像并校验

#### （1）下载镜像

- 官网地址：[Arch Linux 下载页](https://archlinux.org/download/#download-mirrors)
- 选择 China 镜像源（如清华、中科大），下载格式为 `archlinux-YYYY.MM.DD-x86_64.iso` 的镜像（约 1.4GB）

#### （2）镜像校验（必做，避免安装失败）

- Windows：用 PowerShell 执行 `Get-FileHash -Algorithm SHA256 镜像文件路径`
- Linux/macOS：终端执行 `sha256sum 镜像文件路径`
- 对比官网对应镜像的 SHA256 哈希值（官网下载页可查），一致则镜像完好

### 3. 制作启动 U 盘（二选一）

#### （1）Ventoy（推荐，不格式化 U 盘）

1. 下载 Ventoy：[Ventoy 官网](https://www.ventoy.net/cn/download.html)
2. 安装 Ventoy 到 U 盘：打开 Ventoy2Disk.exe，选择 U 盘，点击「安装」（仅首次需要）
3. 拷贝 ISO 镜像：直接将下载的 Arch 镜像文件复制到 U 盘根目录（无需解压）

#### （2）Rufus（传统方式，会格式化 U 盘）

1. 下载 Rufus：[Rufus 官网](https://rufus.ie/zh/)

2. 配置参数：
   
   - 设备：选择目标 U 盘
   - 启动类型选择：「选择」→ 选中下载的 Arch ISO 镜像
   - 分区类型：**GPT**（必须，对应 UEFI）
   - 目标系统类型：「UEFI（非 Legacy）」

3. 点击「开始」，等待制作完成（注意：U 盘数据会被清空）

## 二、引导进入 Arch Live 环境

### 1. 调整 BIOS/UEFI 设置

1. 目标电脑关机，插入制作好的 U 盘

2. 开机时连续按固件引导快捷键（常见：Delete、F2、F8、F12，电脑品牌不同略有差异）

3. 进入 BIOS/UEFI 后：
   
   - 关闭「Secure Boot（安全启动）」（不关闭无法引导 Arch 镜像）
   - 切换「启动模式」为 UEFI（而非 Legacy/CSM）

4. 在 Boot Manager 中，将 U 盘设置为第一启动项（Ventoy 开头或 USB HDD 开头）

5. 按 F10 保存设置并重启

### 2. 进入 Live 环境

- Ventoy 引导：重启后选择「Arch Linux 镜像文件」即可进入
- Rufus 引导：重启后自动进入 Arch Live 环境（黑色终端界面，用户名 `root`，无需密码）

## 三、Live 环境初始化（网络 + 系统准备）

### 1. 验证网络连通性

#### （1）有线网络（自动配置，优先选择）

直接执行 `ping baidu.com`，若返回如下信息则网络正常：

```
ping baidu.com  # 测试网络
# 成功提示：64 字节，来自 xxx.xxx.xxx.xxx: icmp_seq=1 ttl=xx 时间=xx.ms
# 按 Ctrl+C 停止测试
```

#### （2）无线网络（需手动配置）

使用 `iwctl` 工具配置，完整步骤如下（新手直接复制命令，替换括号内容）：

```
iwctl  # 进入 iwctl 交互模式（提示符变为 [iwctl]#）
list devices  # 查看无线网卡名称（通常是 wlan0 或 wlp2s0）
station wlan0 scan  # 扫描附近 WiFi（替换 wlan0 为你的网卡名）
station wlan0 get-networks  # 列出可用 WiFi
station wlan0 connect "WiFi名称"  # 连接 WiFi（输入密码后回车）
exit  # 退出 iwctl 模式
ping baidu.com  # 再次验证网络（确保连接成功）
```

> 排查：若 ping 失败，尝试 `systemctl restart systemd-networkd` 重启网络服务，或禁用 IPv6（`sysctl -w net.ipv6.conf.all.disable_ipv6=1`）

### 2. 配置 Pacman 软件源（加速安装）

#### （1）安装 reflector（自动生成最优源）

```
pacman -Syy reflector  # 安装源排序工具（-Syy 强制更新包数据库）
```

#### （2）生成国内镜像源（替换默认 mirrorlist）

```
reflector --country China --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
# 说明：筛选 12 小时内可用的国内 HTTPS 源，按速度排序
```

#### （3）启用 Pacman 并行下载（可选，加速安装）

编辑 Pacman 配置文件：

```
vim /etc/pacman.conf
```

找到 `ParallelDownloads` 行，修改为：

```
ParallelDownloads = 5  # 并行下载 5 个包（默认 1）
Color  # 开启彩色输出（可选，更易读）
```

按 `Esc` → 输入 `:wq` 保存退出

### 3. 更新系统密钥（避免安装包签名错误）

```
pacman -S archlinux-keyring --refresh-keys
# 说明：更新 Arch 官方密钥环，解决包验证失败问题
```

## 四、分区与挂载（核心步骤，谨慎操作）

### 1. 查看磁盘布局（确定操作目标）

```
lsblk -pf  # 列出所有磁盘和分区（-p 显示绝对路径，-f 显示文件系统）
# 示例输出：/dev/nvme0n1（目标磁盘）、/dev/nvme0n1p1（Windows ESP 分区，vfat 格式）
```

> 识别目标磁盘：通常是 `/dev/nvme0n1`（NVMe 硬盘）或 `/dev/sda`（SATA 硬盘），避免误操作其他磁盘

### 2. 分区方案（推荐，适配单 / 双系统）

| 分区类型        | 大小建议                        | 分区标识（gdisk） | 挂载点   | 用途            |
| ----------- | --------------------------- | ----------- | ----- | ------------- |
| EFI 系统分区    | 512MB                       | EF00        | /boot | 引导文件存储        |
| Swap 交换分区   | 内存≤8G 选 2 倍；内存 > 8G 选 8-16G | 8200        | 无     | 虚拟内存          |
| /（根分区）      | 50-100G                     | 8300        | /     | 系统文件存储        |
| /home（用户分区） | 剩余所有空间                      | 8300        | /home | 用户数据（独立分区易恢复） |

### 3. 使用 gdisk 分区（GPT 格式）

以目标磁盘 `/dev/nvme0n1` 为例（替换为你的磁盘路径）：

```
gdisk /dev/nvme0n1  # 进入 gdisk 分区工具
```

#### （1）新建分区表（新硬盘必做，已有分区需备份后执行）

输入 `o` → 回车确认（清除原有分区表，建立新 GPT 分区表）

#### （2）创建 EFI 分区

1. 输入 `n` → 回车（默认分区号）→ 回车（默认起始扇区）→ 输入 `+512MB`（分区大小）→ 输入 `EF00`（分区标识）→ 回车
2. 输入 `p` 查看分区（确认新分区类型为 `EFI System`）

#### （3）创建 Swap 分区

输入 `n` → 回车 → 回车 → 输入 `+16G`（示例大小）→ 输入 `8200` → 回车

#### （4）创建 /（根分区）

输入 `n` → 回车 → 回车 → 输入 `+80G` → 输入 `8300` → 回车

#### （5）创建 /home 分区

输入 `n` → 回车 → 回车 → 回车（默认使用剩余空间）→ 输入 `8300` → 回车

#### （6）保存分区并退出

输入 `p` 再次确认分区列表（应有 4 个分区）→ 输入 `w` → 回车确认（保存分区表并退出）

> 警告：此步骤不可逆，分区错误会导致数据丢失！

### 4. 格式化分区（替换为你的分区路径）

假设分区路径如下（根据 `lsblk -pf` 实际结果修改）：

- EFI 分区：`/dev/nvme0n1p1`
- Swap 分区：`/dev/nvme0n1p2`
- / 分区：`/dev/nvme0n1p3`
- /home 分区：`/dev/nvme0n1p4`

执行格式化命令：

```
# 格式化 EFI 分区（vfat 格式）
mkfs.fat -F 32 /dev/nvme0n1p1

# 格式化 Swap 分区
mkswap /dev/nvme0n1p2
swapon /dev/nvme0n1p2  # 临时启用 Swap

# 格式化 / 分区（ext4 格式）
mkfs.ext4 /dev/nvme0n1p3

# 格式化 /home 分区（ext4 格式）
mkfs.ext4 /dev/nvme0n1p4
```

### 5. 挂载分区（按顺序挂载）

```
# 1. 挂载 / 分区到 /mnt（核心挂载点）
mount /dev/nvme0n1p3 /mnt

# 2. 创建 /boot、/home 目录并挂载
mount --mkdir /dev/nvme0n1p1 /mnt/boot  # 挂载 EFI 分区
mount --mkdir /dev/nvme0n1p4 /mnt/home  # 挂载 /home 分区

# 3. 验证挂载结果
lsblk -pf  # 确认所有分区挂载点正确
```

> 双系统注意：不要挂载 Windows 的 ESP 分区（通常是 100MB 左右的 vfat 分区），避免覆盖引导！

## 五、安装 Arch 基础系统

### 1. 执行 pacstrap 安装核心包

```
pacstrap -K /mnt base base-devel linux linux-firmware \
efibootmgr grub networkmanager vim pipewire bluez bluez-utils \
ntp wget curl  # 核心包+常用工具
```

#### 包说明（按需增减）

| 包名                  | 用途                   | 是否必装 |
| ------------------- | -------------------- | ---- |
| base + base-devel   | 基础系统 + 编译工具链         | 是    |
| linux / linux-lts   | 主线内核 / 长期支持内核        | 二选一  |
| linux-firmware      | 硬件驱动固件               | 是    |
| efibootmgr + grub   | UEFI 引导管理 + GRUB 菜单  | 是    |
| networkmanager      | 网络管理（自动连接 WiFi / 有线） | 是    |
| pipewire            | 音频服务                 | 是    |
| bluez + bluez-utils | 蓝牙驱动 + 工具            | 可选   |

#### 常见报错处理

- 报错 `vconsole.conf not found`：创建配置文件后重试
  
  ```
  echo "KEYMAP=us" > /mnt/etc/vconsole.conf
  pacstrap -K /mnt ...  # 重新执行安装命令
  ```

- 包下载失败：重新执行 `reflector` 生成源，或换镜像源

### 2. 生成 fstab（自动挂载分区，必做）

```
genfstab -U /mnt > /mnt/etc/fstab  # -U 按 UUID 挂载（更稳定）
```

#### 验证 fstab（避免挂载错误）

```
cat /mnt/etc/fstab  # 查看生成的文件
# 确保包含 /、/boot、/home、swap 的挂载记录
```

### 3. 进入新系统（arch-chroot）

```
arch-chroot /mnt  # 切换到新安装的系统环境（后续操作均在该环境下）
```

## 六、系统初始化配置（必做）

### 1. 配置时区与时间同步

```
# 1. 设置时区（上海）
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 2. 同步系统时间到硬件时钟
hwclock -w

# 3. 启用 NTP 时间同步（自动校准时间）
timedatectl set-ntp true
timedatectl status  # 验证（显示 NTP 服务开启）
```

### 2. 配置系统语言

#### （1）生成 locale

```
vim /etc/locale.gen  # 编辑语言配置文件
```

取消以下两行的注释（删除开头的 `#`）：

```
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
```

按 `Esc` → `:wq` 保存，执行生成命令：

```
locale-gen  # 生成语言包
```

#### （2）设置默认语言（避免 TTY 乱码）

```
echo "LANG=en_US.UTF-8" > /etc/locale.conf
# 说明：桌面环境安装后可改为 zh_CN.UTF-8，当前用英文避免 TTY 乱码
```

### 3. 配置主机名与 hosts（可选）

#### （1）设置主机名（自定义，如 myarch）

```
echo "myarch" > /etc/hostname  # 替换 myarch 为你的主机名
```

#### （2）配置 hosts 文件

```
vim /etc/hosts
```

添加以下内容（替换 myarch 为你的主机名）：

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   myarch.localdomain myarch
```

### 4. 设置用户与权限

#### （1）设置 root 密码（必须，用于紧急登录）

```
passwd root  # 输入密码（无回显，输入两次确认）
```

#### （2）创建普通用户（日常使用，避免直接用 root）

```
useradd -mG wheel 用户名  # -m 自动创建 /home/用户名 目录；-G wheel 加入 wheel 组
passwd 用户名  # 设置普通用户密码
```

#### （3）启用 sudo 权限（允许普通用户执行管理员命令）

```
visudo  # 编辑 sudo 配置文件
```

找到以下行，**取消 `%wheel ALL=(ALL:ALL) ALL` 前的注释**（保留 `%`，代表组权限）：

```
root ALL=(ALL:ALL) ALL
%wheel ALL=(ALL:ALL) ALL  # 取消开头的 # 即可
```

按 `Esc` → `:wq!` 强制保存退出（visudo 不允许普通保存）

### 5. 安装显卡驱动（必做，否则桌面黑屏）

根据显卡型号选择对应的驱动：

#### （1）Intel 核显

```
pacman -S mesa vulkan-intel
```

#### （2）AMD 显卡

```
pacman -S mesa vulkan-radeon
```

#### （3）NVIDIA 独显（需安装内核头文件）

```
pacman -S linux-headers nvidia-dkms  # 若用 linux-lts 内核，替换为 linux-lts-headers
```

### 6. 配置 GRUB 引导（双系统 + 单系统通用）

#### （1）安装 GRUB 到 EFI 分区

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Arch --recheck
# 说明：--bootloader-id=Arch 是 GRUB 菜单中的系统名称
```

#### （2）双系统配置（识别 Windows）

1. 安装 os-prober（系统识别工具）：
   
   ```
   pacman -S os-prober
   ```

2. 编辑 GRUB 配置，启用 os-prober：
   
   ```
   vim /etc/default/grub
   ```
   
   找到 `GRUB_DISABLE_OS_PROBER=false`，取消注释（若无则添加该行）：
   
   ```
   GRUB_DISABLE_OS_PROBER=false  # 启用双系统识别
   GRUB_TIMEOUT=5  # 引导菜单停留 5 秒（可自定义）
   ```

3. 检测 Windows 系统：
   
   ```
   os-prober  # 成功会显示 Windows 引导路径（如 /dev/nvme1n1p1@/EFI/Microsoft/Boot/bootmgfw.efi）
   ```

#### （3）生成 GRUB 配置文件

```
grub-mkconfig -o /boot/grub/grub.cfg  # 生成引导菜单
```

> 验证：执行后会显示 Arch 和 Windows（双系统）的引导项，代表成功

### 7. 配置输入法（中文输入）

```
# 安装 fcitx5 输入法套件
pacman -S fcitx5 fcitx5-rime fcitx5-chinese-addons fcitx5-configtool fcitx5-gtk fcitx5-qt
```

#### 配置输入法环境变量（必做，否则输入法不生效）

```
vim /etc/environment  # 编辑环境变量文件
```

添加以下内容（无 `env =` 前缀，直接键值对）：

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
FCITX_IM_MODULE=fcitx
```

按 `Esc` → `:wq` 保存

### 8. 启用基础服务（开机自启）

```
# 网络管理服务（必启）
systemctl enable NetworkManager

# 蓝牙服务（可选）
systemctl enable bluetooth

# 音频服务（必启）
systemctl enable pipewire pipewire-pulse
```

## 七、安装桌面环境（三选一，新手推荐 KDE/xfce4）

### 1. KDE Plasma（功能丰富，美观易用）

```
# 精简安装（避免冗余软件）
pacman -S plasma-desktop sddm dolphin konsole  # 核心桌面+登录管理器+文件管理器+终端
```

启用登录管理器（开机自动进入桌面）：

```
systemctl enable sddm
```

### 2. Xfce4（轻量稳定，适合老电脑 / 新手）

```
pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter
```

启用登录管理器：

```
systemctl enable lightdm
```

### 3. GNOME（简洁现代，Wayland 优先）

```
pacman -S gnome gnome-extra gdm
```

启用登录管理器：

```
systemctl enable gdm
```

## 八、完成安装与后续优化

### 1. 退出 chroot 并重启

```
exit  # 退出 arch-chroot 环境
umount -R /mnt  # 卸载所有挂载的分区
reboot  # 重启电脑（重启时拔掉 U 盘）
```

重启后会进入 GRUB 引导菜单，选择「Arch Linux」即可进入桌面环境

### 2. 桌面环境基础配置

#### （1）中文环境设置

- KDE：设置 → 区域设置 → 语言 → 选择「中文（中国）」→ 应用（需下载语言包，自动安装）
- Xfce4：设置 → 语言支持 → 选择「中文（中国）」→ 注销后生效
- GNOME：设置 → 区域与语言 → 语言 → 选择「中文（中国）」→ 重启

#### （2）输入法配置

打开「Fcitx5 配置工具」→ 点击「+」→ 取消「只显示当前语言」→ 选择「Rime」→ 配置输入法（如切换拼音 / 五笔）

### 3. 安装 AUR 助手（可选，安装第三方软件）

```
# 安装 yay（最常用 AUR 助手）
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si  # 编译安装（需输入密码）
```
### 4. 常用软件安装（可选）
```
# 浏览器（Chrome）
yay -S google-chrome

# 办公软件（LibreOffice）
pacman -S libreoffice-fresh libreoffice-fresh-zh-CN

# 截图工具
pacman -S spectacle  # KDE 自带；Xfce4 用 xfce4-screenshooter；GNOME 用 gnome-screenshot
```
## 九、常见故障排查
### 1. 开机无法进入 GRUB 菜单
- 原因：EFI 分区挂载错误或 GRUB 安装失败
- 解决：用 U 盘重新进入 Live 环境，arch-chroot 后重新执行 `grub-install` 和 `grub-mkconfig`
### 2. 桌面黑屏（无登录界面）
- 原因：显卡驱动未安装或安装错误
- 解决：Ctrl+Alt+F2 进入 TTY，登录后重新安装对应显卡驱动
### 3. 输入法无法切换
- 原因：环境变量未配置或配置错误
- 解决：重新检查 `/etc/environment` 中的输入法配置，注销后重试
### 4. 双系统无法识别 Windows
- 原因：os-prober 未启用或 Windows ESP 分区未挂载
- 解决：arch-chroot 后重新安装 os-prober，确保 `GRUB_DISABLE_OS_PROBER=false`，重新生成 GRUB 配置
