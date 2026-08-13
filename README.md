# OpenWrt Athena LED Controller

[English](#english) | 简体中文

---

## 🇨🇳 简体中文

适用于京东云无线宝 AX6600 (雅典娜) 的终极 LED 点阵屏控制器。

本项目基于 haipengno1 和 NONGFAH 的作品进行了深度开发，将核心程序与 LuCI 界面整合，实现了丰富的新功能。

### ✨ 核心功能

- **网络监控**: 实时上下行网速、WAN IP、ARP 在线设备数、连接数、TCP 延迟
- **系统状态**: CPU/内存占用率、系统运行时间、温度监控与超温闪烁告警
- **日历与天气**: 当地天气、农历日期、日出日落、倒数日 (D-Day)
- **极致休眠**: 零负载精准休眠 + 夜间自动降亮度
- **自动化集成**: MQTT 消息上屏 (Home Assistant)、本机控制接口 (nc 一行指令插播文本/切台/调亮度)
- **全固件兼容**: GPIO 双后端自动适配 (官方 OpenWrt / QWRT / iStoreOS)，LuCI JS 界面
- **交互**: 物理按键短按切台 / 双击回首页 / 长按息屏

---

### 📥 安装方法

> 🌟 v2.3.0 起拆分为两个软件包：`athena-led`(核心驱动) + `luci-app-athena-led`(Web 界面)，**两个都要装**。

#### 🅰️ 方案一：直接下载安装（推荐）

前往 [Releases 页面](https://github.com/guangyin53222/AX6600-Athena-LED-Controller/releases)，根据固件版本下载对应包：

**OpenWrt 23.05 / 24.10（使用 .ipk）**
```bash
opkg install /tmp/athena-led_*.ipk /tmp/luci-app-athena-led_*.ipk
```

**OpenWrt Snapshot（使用 .apk）**
```bash
apk add --allow-untrusted /tmp/athena-led_*.apk /tmp/luci-app-athena-led_*.apk
```

安装完成后刷新网页，进入 **服务 (Services) → Athena LED** 进行配置。

---

#### 🅱️ 方案二：从 Feed 编译拉取（开发者/固件编译）

如果你想在编译 OpenWrt 固件时自动拉取并编译本插件，只需三步：

**Step 1：添加 Feed 源**

在 OpenWrt 源码根目录，编辑 `feeds.conf`（或 `feeds.conf.default`），加入一行：

```bash
echo "src-git athena https://github.com/guangyin53222/AX6600-Athena-LED-Controller.git;main" >> feeds.conf
```

> 💡 末尾的 `;main` 指定分支，也可以锁定版本：`;v2.5.0`

**Step 2：更新并安装**

```bash
./scripts/feeds update athena
./scripts/feeds install -a -p athena
```

**Step 3：在 menuconfig 中选择**

```bash
make menuconfig
```

按以下路径找到并选中（按 `Y` 勾选）：

```
Utilities  --->
    <*> athena-led  (核心驱动)

LuCI  --->
    3. Applications  --->
        <*> luci-app-athena-led  (Web 界面)
```

然后正常编译固件即可：

```bash
make -j$(nproc)
```

编译完成后，包会在 `bin/packages/aarch64_cortex-a53/athena/` 目录下。

---

#### 🔧 方案三：使用 OpenWrt SDK 单独编译

如果你不想编译整个固件，只想编译这两个包：

```bash
# 1. 下载对应你路由器的 OpenWrt SDK
# 2. 在 SDK 根目录添加 feed
echo "src-git athena https://github.com/guangyin53222/AX6600-Athena-LED-Controller.git;main" >> feeds.conf

# 3. 更新并安装
./scripts/feeds update athena
./scripts/feeds install athena-led luci-app-athena-led

# 4. 编译
make package/athena-led/compile V=s
make package/luci-app-athena-led/compile V=s

# 5. 产物在 bin/packages/ 下
ls bin/packages/*/athena/
```

---

### 🏗️ 开发者：从源码编译 Rust 核心

目标架构：`aarch64-unknown-linux-musl`

**方法 1：Docker（推荐）**
```bash
cd athena-led
./scripts/aarch64-unknown-linux-musl-build.sh
# 产物：output/aarch64-unknown-linux-musl/athena-led
```

**方法 2：Cargo Cross**
```bash
cd athena-led
cross build --target aarch64-unknown-linux-musl --release
# 产物：target/aarch64-unknown-linux-musl/release/athena-led
```

---

### 📜 致谢

- 核心逻辑基于 [NONGFAH/athena-led](https://github.com/NONGFAH/athena-led)
- LuCI 界面基于 [haipengno1/luci-app-athena-led](https://github.com/haipengno1/luci-app-athena-led)
- 增强功能由 unraveloop & Team 实现

### 📄 许可证

Apache License 2.0

---

## English

The ultimate LED matrix controller for JDCloud AX6600 (Athena), featuring a comprehensive LuCI interface and extensive system monitoring.

This project is a heavily modified fork based on haipengno1 and NONGFAH, with integrated backend and frontend plus significant new features.

### ✨ Key Features

- Network monitoring: real-time speed, WAN IP, ARP device count, connection count, TCP latency
- System status: CPU/RAM usage, uptime, temperature with overheat alert
- Weather & calendar: local weather, lunar date, sunrise/sunset, D-Day countdown
- Zero-load precision sleep + auto dimming
- MQTT integration & local control interface (nc commands)
- GPIO dual-backend auto-detection (cdev/sysfs)
- Physical button: short press switch / double click home / long press sleep

### 📥 Installation

#### Option A: Download from Releases (Recommended)

Download `athena-led_*.ipk` + `luci-app-athena-led_*.ipk` (or `.apk` for snapshot) from the [Releases page](https://github.com/guangyin53222/AX6600-Athena-LED-Controller/releases).

**OpenWrt 23.05 / 24.10 (opkg)**
```bash
opkg install /tmp/athena-led_*.ipk /tmp/luci-app-athena-led_*.ipk
```

**OpenWrt Snapshot (apk)**
```bash
apk add --allow-untrusted /tmp/athena-led_*.apk /tmp/luci-app-athena-led_*.apk
```

#### Option B: Build from Feed (Firmware Integration)

**Step 1: Add feed source**

In your OpenWrt source root, add to `feeds.conf`:

```bash
echo "src-git athena https://github.com/guangyin53222/AX6600-Athena-LED-Controller.git;main" >> feeds.conf
```

**Step 2: Update and install**

```bash
./scripts/feeds update athena
./scripts/feeds install -a -p athena
```

**Step 3: Select in menuconfig**

```
Utilities  --->
    <*> athena-led

LuCI  --->
    3. Applications  --->
        <*> luci-app-athena-led
```

Then build: `make -j$(nproc)`

#### Option C: SDK Standalone Build

```bash
echo "src-git athena https://github.com/guangyin53222/AX6600-Athena-LED-Controller.git;main" >> feeds.conf
./scripts/feeds update athena
./scripts/feeds install athena-led luci-app-athena-led
make package/athena-led/compile V=s
make package/luci-app-athena-led/compile V=s
```

### 🏗️ Build Rust Core from Source

Target: `aarch64-unknown-linux-musl`

**Docker (Recommended)**
```bash
cd athena-led
./scripts/aarch64-unknown-linux-musl-build.sh
```

**Cargo Cross**
```bash
cd athena-led
cross build --target aarch64-unknown-linux-musl --release
```

### 📜 Credits

- Core logic: [NONGFAH/athena-led](https://github.com/NONGFAH/athena-led)
- LuCI base: [haipengno1/luci-app-athena-led](https://github.com/haipengno1/luci-app-athena-led)
- Enhanced features: unraveloop & Team

### 📄 License

Apache License 2.0
