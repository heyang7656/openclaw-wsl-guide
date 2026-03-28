# Windows 11 环境下 WSL 安装与配置完整指南

本教程详细介绍在 Windows 11 上安装和配置 WSL（Windows Subsystem for Linux）的完整流程，为后续安装 Opencode 和 OpenClaw 打下基础。

## 前置条件

在开始安装之前，请确保你的系统满足以下要求：

- **操作系统**：Windows 11 版本 22000 或更高（建议更新到最新版本）
- **BIOS/UEFI**：已启用虚拟化技术（Intel VT-x / AMD-V）
- **内存**：至少 4GB 可用内存（推荐 8GB 或以上）
- **磁盘空间**：至少 20GB 可用磁盘空间
- **网络连接**：稳定的网络连接（用于下载组件）
- **管理员权限**：需要 Windows 管理员权限以启用 WSL 功能

**预计安装时间**：约 15-20 分钟（取决于网络速度）

---

## 目录

- [一、WSL 简介](# 一 wsl-简介)
- [二、WSL 安装步骤](# 二 wsl-安装步骤)
- [三、WSL 网络架构与配置](# 三 wsl-网络架构与配置)
- [四、常见问题 A&Q](#四常见问题-aq)
- [五、卸载指南](#五卸载指南)
- [六、性能调优建议](#六性能调优建议)

---

## 一、WSL 简介

### 什么是 WSL？

**WSL（Windows Subsystem for Linux，适用于 Linux 的 Windows 子系统）** 是微软推出的一项兼容层技术，允许用户在 Windows 系统上直接运行 Linux 应用程序，而无需使用虚拟机或双系统。

### WSL 的两个版本

#### WSL 1（早期版本）
- **技术原理**：采用系统调用转换层，将 Linux 系统调用转换为 Windows NT 内核调用
- **优点**：启动速度快，兼容 Windows 文件系统
- **缺点**：不支持完整的 Linux 内核功能，Docker 等工具兼容性差

#### WSL 2（推荐版本）
- **技术原理**：运行完整的 Linux 内核（基于 Hyper-V 虚拟化技术）
- **优点**：
  - 完整的 Linux 内核支持
  - 更好的性能表现（尤其是文件操作）
  - 完整的 Docker 支持
  - 更好的系统调用兼容性
- **缺点**：占用资源略多，启动稍慢

**强烈建议使用 WSL 2**，因为它提供了完整的内核支持和更好的性能，特别适合开发环境和 AI 工具部署。

### WSL 的网络模式

WSL 2 有两种网络模式：

#### NAT 模式（默认）
- WSL 2 拥有独立的虚拟 IP 地址（172.x.x.x 范围）
- 外部网络无法直接访问 WSL 2
- 需要端口转发才能实现局域网访问
- VPN 兼容性较差，经常出现 DNS 解析问题

#### 镜像模式（强烈推荐）
- 与宿主机共享相同的局域网 IP
- 原生 IPv6 支持
- VPN 兼容性显著提升
- 局域网内其他设备可直接访问
- 防火墙规则直接作用于 WSL 应用

**本教程推荐使用镜像模式**，可以大幅提升网络兼容性和访问便利性。

---

## 二、WSL 安装步骤

### 2.1 一键安装 WSL

微软在 Windows 11 中极大地简化了安装流程，通过单一命令即可完成核心组件的启用。

**步骤 1**：以管理员身份打开 PowerShell 或 Windows 终端

在开始菜单中搜索 "PowerShell"，右键选择"以管理员身份运行"。

**步骤 2**：执行安装命令

```powershell
wsl --install
```

此操作将自动执行以下逻辑：

1. **启用虚拟机平台（Virtual Machine Platform）** - WSL 2 的底层虚拟化技术
2. **启用适用于 Linux 的 Windows 子系统** - WSL 核心组件
3. **下载并安装最新的 Linux 内核** - WSL 2 需要的完整内核
4. **默认下载并安装 Ubuntu 发行版** - 主流的 Linux 发行版

**步骤 3**：重启计算机

安装完成后，必须**重启计算机**以使 Hyper-V 管理程序生效。

### 2.2 验证安装状态

重启后，通过以下命令验证状态：

```powershell
wsl --status
wsl --version
```

**预期输出示例**：

```
WSL 版本：2.0.xxxx.0
内核版本：5.15.xxxx
WSLg 版本：1.0.xxxx
MSRDC 版本：1.3.xxxx
Direct3D 版本：1.611.xxxx
DXCore 版本：10.0.xxxx
Windows 版本：10.0.22631.xxxx
```

确认当前 WSL 版本为 2.0 以上。

### 2.3 现有 WSL 用户更新

如果系统中已存在旧版 WSL，建议运行以下命令更新：

```powershell
wsl --update
```

此命令将下载并安装最新的 Linux 内核，确保镜像网络模式所需的内核组件已就绪。

### 2.4 Ubuntu 初始化配置

**步骤 1**：首次启动 Ubuntu

可以通过以下方式启动 Ubuntu：
- 在开始菜单中找到 "Ubuntu" 并点击
- 或在 PowerShell 中输入 `wsl` 命令

**步骤 2**：创建 UNIX 用户名和密码

首次启动时，系统会提示创建账户：

```
Installing, this may take a few minutes...
Please create a default UNIX user account. The username and password must not match your Windows username.
New UNIX username: heyang
New password:
Retype password:
```

**重要说明**：
- 用户名不能使用大写字母
- 密码输入时不会显示（Linux 安全特性）
- 此账户拥有 `sudo` 管理员权限

### 2.5 系统更新与软件源配置

**步骤 1**：配置国内镜像源（推荐）

为了提高下载速度，建议更换为国内镜像源。编辑源配置文件：

```bash
sudo nano /etc/apt/sources.list
```

使用**清华大学 TUNA 镜像源**（Ubuntu 22.04）：

```
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
```

保存退出：`Ctrl+O` → `Enter` → `Ctrl+X`

**步骤 2**：更新系统

```bash
# 更新软件包索引
sudo apt update

# 升级已安装的软件包
sudo apt upgrade -y

# 安装常用工具
sudo apt install -y curl wget git build-essential
```

---

## 三、WSL 网络架构与配置

### 3.1 配置.wslconfig 文件（启用镜像模式）

镜像模式属于全局配置，需在 Windows 用户目录下创建或修改 `.wslconfig` 文件。

**步骤 1**：打开文件资源管理器，导航到以下路径：

```
C:\Users\<您的用户名>\
```

例如：`C:\Users\heyang\`

**步骤 2**：创建或编辑 `.wslconfig` 文件

如果文件不存在，右键 → 新建 → 文本文档，命名为 `.wslconfig`（注意前面的点号）。

**步骤 3**：使用记事本打开文件，添加以下配置：

```ini
[wsl2]
# 启用镜像网络模式 - 这是最重要的配置
networkingMode=mirrored
# 启用 DNS 隧道，防止 VPN 环境下域名解析失效
dnsTunneling=true
# 强制 WSL 使用 Windows 的 HTTP 代理设置
autoProxy=true
# 启用集成防火墙支持
firewall=true

[experimental]
# 自动回收闲置内存，优化性能
autoMemoryReclaim=gradual
# 支持主机回环地址访问
hostAddressLoopback=true
```

**步骤 4**：保存文件并关闭。

**步骤 5**：应用配置

在 Windows PowerShell 中执行：

```powershell
wsl --shutdown
```

**步骤 6**：等待约 8 秒钟以确保虚拟机彻底关闭，然后重新启动 Ubuntu。

### 3.2 验证网络模式配置

进入 WSL 后，执行以下命令验证：

```bash
# 查看网络接口
ip addr show eth0

# 查看路由表
ip route show

# 测试与局域网的连通性
ping 192.168.1.1
```

**成功标志**：你应该能够看到 WSL 使用与 Windows 相同的局域网 IP 地址段（如 192.168.x.x）。

### 3.3 配置 Windows 防火墙

在镜像模式下，WSL2 应用将直接暴露在 Windows 防火墙规则中。为了确保后续安装的服务能够被正确访问，需要配置防火墙规则。

**步骤 1**：以管理员身份打开 PowerShell

**步骤 2**：创建防火墙规则

```powershell
# 创建入站防火墙规则，允许后续 OpenClaw 服务端口
New-NetFirewallRule -DisplayName "OpenClaw-Service" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 18789

# 验证规则是否创建成功
Get-NetFirewallRule -DisplayName "OpenClaw-Service" | Format-Table
```

**步骤 3**：测试端口连通性

```powershell
# 测试本地端口
Test-NetConnection -ComputerName localhost -Port 18789
```

---

## 四、常见问题 A&Q

### Q1: WSL 安装失败怎么办？

**问题现象**：执行 `wsl --install` 后报错

**解决方案**：

1. **检查虚拟化是否启用**：
   - 打开任务管理器（`Ctrl+Shift+Esc`）
   - 切换到"性能" → "CPU"
   - 查看"虚拟化"是否显示为"已启用"
   - 如未启用，需进入 BIOS/UEFI 设置开启

2. **手动启用 WSL 功能**：
   ```powershell
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```

3. **检查 Windows 版本**：
   ```powershell
   winver
   ```
   确保版本不低于 22000

### Q2: Ubuntu 启动后卡在黑屏界面？

**问题现象**：打开 Ubuntu 后长时间停留在黑屏或闪烁光标

**解决方案**：

1. **等待首次初始化完成**：首次启动可能需要 2-5 分钟
2. **重置 WSL**：
   ```powershell
   wsl --unregister Ubuntu
   wsl --install
   ```
3. **检查磁盘空间**：确保 C 盘有足够空间（至少 10GB）

### Q3: 无法访问外网或下载速度慢？

**问题现象**：`apt update` 报错连接超时

**解决方案**：

1. **更换国内镜像源**（见 2.5 节）
2. **检查 DNS 配置**：
   ```bash
   cat /etc/resolv.conf
   ```
   如不正常，可手动设置：
   ```bash
   sudo nano /etc/resolv.conf
   ```
   添加：
   ```
   nameserver 8.8.8.8
   nameserver 1.1.1.1
   ```

### Q4: WSL 占用内存过高如何优化？

**问题现象**：WSL2 占用大量内存导致系统变慢

**解决方案**：

1. **配置内存限制**（在.wslconfig 中）：
   ```ini
   [wsl2]
   memory=4GB
   swap=2GB
   ```

2. **启用自动回收**：
   ```ini
   [experimental]
   autoMemoryReclaim=gradual
   ```

3. **手动释放内存**：
   ```bash
   echo 3 | sudo tee /proc/sys/vm/drop_caches
   ```

4. **重启 WSL**：
   ```powershell
   wsl --shutdown
   ```

### Q5: 如何在 WSL 中访问 Windows 文件？

**使用方法**：

Windows 的驱动器会自动挂载到 `/mnt/` 目录下：

```bash
# 访问 C 盘
cd /mnt/c/Users/heyang/

# 访问 D 盘
cd /mnt/d/

# 快速跳转到 Windows 桌面
cd /mnt/c/Users/heyang/Desktop/
```

### Q6: VPN 环境下 DNS 解析问题？

**问题现象**：连接 VPN 后 WSL 无法解析域名

**解决方案**：

1. **启用 DNS 隧道**（已在.wslconfig 中配置）：
   ```ini
   [wsl2]
   dnsTunneling=true
   ```

2. **防止自动覆盖**：
   ```bash
   sudo chattr +i /etc/resolv.conf
   ```

### Q7: 如何备份 WSL 环境？

**备份方法**：

```powershell
# 在 Windows PowerShell 中执行

# 导出为 tar 文件
wsl --export Ubuntu D:\backup\ubuntu-backup.tar

# 恢复时
wsl --import Ubuntu D:\WSL\Ubuntu D:\backup\ubuntu-backup.tar --version 2
```

### Q8: 如何切换 WSL 版本？

**查看当前版本**：
```powershell
wsl --list --verbose
```

**切换到 WSL 2**：
```powershell
wsl --set-version Ubuntu 2
```

**设置默认版本**：
```powershell
wsl --set-default-version 2
```

---

## 五、卸载指南

### 5.1 卸载 Ubuntu 发行版

**方法一**：保留数据卸载（可重新安装）

```powershell
# 在 Windows PowerShell 中执行
wsl --unregister Ubuntu
```

**方法二**：通过设置卸载

1. 打开 Windows 设置 → 应用 → 安装的应用
2. 搜索 "Ubuntu"
3. 点击"卸载"

### 5.2 完全卸载 WSL

**步骤 1**：卸载所有 Linux 发行版

```powershell
# 列出所有发行版
wsl --list --verbose

# 逐个卸载
wsl --unregister <发行版名称>
```

**步骤 2**：禁用 WSL 功能

以管理员身份运行 PowerShell：

```powershell
dism.exe /online /disable-feature /featurename:Microsoft-Windows-Subsystem-Linux /norestart
dism.exe /online /disable-feature /featurename:VirtualMachinePlatform /norestart
```

**步骤 3**：删除配置文件

删除以下文件（如果存在）：
- `C:\Users\<你的用户名>\.wslconfig`
- `C:\Users\<你的用户名>\AppData\Local\Packages\CanonicalGroupLimited*`

**步骤 4**：重启计算机

使更改生效。

### 5.3 恢复防火墙设置

```powershell
# 删除 OpenClaw 相关规则
Remove-NetFirewallRule -DisplayName "OpenClaw-*"

# 验证规则已删除
Get-NetFirewallRule -DisplayName "*OpenClaw*"
```

### 5.4 清理残留文件

```powershell
# 删除 WSL 相关文件夹
Remove-Item -Path "$env:LOCALAPPDATA\Packages\CanonicalGroupLimited*" -Recurse -Force
```

---

## 六、性能调优建议

### 6.1 WSL2 资源配置

在 `.wslconfig` 文件中优化性能配置：

```ini
[wsl2]
# 内存限制（根据物理内存调整）
memory=8GB

# Swap 空间
swap=4GB

# CPU 核心数（不超过物理核心数）
processors=4

# 启用嵌套虚拟化（如需运行 Docker）
nestedVirtualization=true

[experimental]
# 自动回收闲置内存
autoMemoryReclaim=gradual

# 使用稀疏 VHD 减少磁盘占用
sparseVhd=true
```

### 6.2 网络加速建议

针对国内用户的下载加速：

1. **使用国内镜像源**：
   - 清华大学 TUNA：https://mirrors.tuna.tsinghua.edu.cn/
   - 阿里云：https://mirrors.aliyun.com/
   - 中科大：https://mirrors.ustc.edu.cn/

2. **配置 Git 镜像**：
   ```bash
   git config --global url."https://ghproxy.com/".insteadOf "https://github.com/"
   ```

3. **使用代理**（如有）：
   ```bash
   export http_proxy=http://127.0.0.1:7890
   export https_proxy=http://127.0.0.1:7890
   ```

### 6.3 定期维护建议

**每周执行一次**：

```bash
# 清理 apt 缓存
sudo apt clean
sudo apt autoremove -y

# 清理临时文件
rm -rf /tmp/*
rm -rf ~/.cache/*

# 检查磁盘使用
df -h
du -sh ~/*
```

**每月执行一次**：

```powershell
# 在 Windows 中压缩 WSL 虚拟磁盘
wsl --shutdown
diskpart
# 在 diskpart 中选择对应的 vhdx 文件并压缩
```

### 6.4 备份策略

**自动化备份脚本**（保存为 `backup-wsl.sh`）：

```bash
#!/bin/bash
BACKUP_DIR=/mnt/d/WSL-Backups
DATE=$(date +%Y%m%d)
mkdir -p $BACKUP_DIR
wsl --export Ubuntu $BACKUP_DIR/ubuntu-backup-$DATE.tar.gz
echo "Backup completed: $BACKUP_DIR/ubuntu-backup-$DATE.tar.gz"
```

**定时备份**（每周日凌晨 2 点）：

```bash
crontab -e
# 添加：
0 2 * * 0 /home/heyang/backup-wsl.sh
```

---

## 结语

恭喜你完成 WSL 的安装和配置！现在你已经拥有了一个功能完整的 Linux 开发环境。

**下一步**：
- 继续阅读《Opencode 安装与使用教程》
- 学习如何在 WSL 中配置开发环境
- 探索 Linux 基础命令和工作流程

**官方资源**：
- WSL 官方文档：https://learn.microsoft.com/zh-cn/windows/wsl/
- Ubuntu 文档：https://ubuntu.com/server/docs
- WSL GitHub Issues：https://github.com/microsoft/WSL/issues
