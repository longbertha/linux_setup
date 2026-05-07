<div align="center">

# **Linux 服务器一键配置与优化脚本**
面向 VPS/云主机/物理机的「一键式」系统与网络栈调优，兼顾可逆与幂等，适合持续运行在生产环境。可根据不同系统自行安装fail2ban等常用软件，可对服务器进行内存、内核和网络优化，可进行安全加固。


</div>

<p align="center">
  <img src="https://img.shields.io/badge/platform-Debian%20%7C%20Ubuntu%20%7C%20CentOS%20%7C%20Fedora-blue" alt="平台兼容性徽章">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="许可证徽章">
  <a href="https://github.com/longbertha/linux_setup">
    <img src="https://img.shields.io/github/stars/longbertha/linux_setup?style=social" alt="GitHub stars">
  </a>
</p>

---


-   **环境自适应，而非写死命令**
    脚本内置了环境检测机制，能自动识别你的操作系统（Debian/Ubuntu, CentOS, Fedora, Arch）和防火墙（UFW, Firewalld, iptables, nftables）。只管选择功能，脚本会用最适合当前环境的方式去执行。

-   **性能优化，而非无脑复制**
    网上很多优化方案都是直接复制粘贴一堆参数，但每个服务器的网络环境都不同。我的优化脚本会引导你输入服务器的实际延迟和带宽，**动态计算带宽延迟积（BDP）**，为每一台服务器量身定制 TCP 缓冲区大小。这才是真正有效的优化。


---
## 🚀 主要功能

<strong>Ⅰ. 基础环境 & 安全设置</strong>

| 图标 | 功能 | 描述 |
| :--: | :--- | :--- |
| 📦 | **安装常用组件** | 一键装好 Fail2ban 等常用的东西，省得一个个 `apt install` 了。 |
| 🔑 | **添加 SSH 公钥** | 把你的公钥加进去，以后就能免密登录，方便又安全。 |
| 🛡️ | **关闭密码登录** | 安全第一。关掉密码登录，只用密钥，能防掉绝大多数脚本小子。 |
| 🚪 | **修改 SSH 端口** | 默认的 22 端口天天被扫，换个不常用的清净点。 |
| 🔥 | **统一防火墙管理** | 自动识别并适配防火墙，提供统一的端口操作界面。 |
| 🌐 | **配置公共 DNS** | 换上 CF 和谷歌的 DNS，解析又快又稳。 |


<strong>Ⅱ. 性能 & 资源优化</strong>

| 图标 | 功能 | 描述 |
| :--: | :--- | :--- |
| 💾 | **设置 Swap** | 小内存 VPS 救星。搞个 Swap，防止内存一满就死机。 |
| ⚡ | **配置 ZRAM** | Swap 的 Pro Max 版。在内存里搞压缩交换，速度飞快，高负载下体验提升明显。 |
| 📊 | **修改 Swappiness** | 让系统别那么爱用 Swap，物理内存多的时候就别去碰硬盘了。 |
| 🧹 | **清理 Swap 缓存** | 手动把 Swap 里的东西倒回内存，看着清爽。 |
| 🚀 | **优化内核参数** | 优化内存占用，启用 BBR+FQ，并根据你的网络环境动态计算 BDP，量身定制内核参数优化方案。 |

<strong>III. 网络 & 内核优化</strong>

| 分类 | 功能 | 效果 |
|:---|:---|:---|
| **拥塞控制** | BBR + FQ 队列调度 | 提升吞吐量，降低延迟，尤其在丢包网络中效果显著 |
| **连接优化** | TCP 快速打开 (TFO) | 减少连接建立时间，加速短连接 |
|  | TIME_WAIT 优化 | 快速回收端口，支持高并发服务器 |
| **缓冲区调优** | 动态内存缓冲区 | 根据系统内存自动调整 TCP/UDP 缓冲区大小 |
| **系统响应** | 内存管理优化 | 优化 swappiness 和 dirty_ratio，优先保障应用性能 |
|  | 连接跟踪优化 | 大幅增加 nf_conntrack_max，防止高负载下连接被丢弃 |
| **硬件优化** | IRQ 亲和性 & Offload | 智能绑定网卡中断到指定 CPU，关闭问题 offload |
| **易用性** | 幂等 & 可逆 | 可重复执行，提供完整卸载功能 |
|  | 广泛兼容 | 支持主流 Linux 发行版和虚拟化平台 |
---


<a id="benchmarks"></a>

## 📈 性能提升

> ⚠️ **免责声明**：下列数字来自作者单机测试，**仅为示意值**，不构成任何性能承诺。实际效果强烈依赖硬件、网卡驱动、对端链路、拥塞状态、流量模型等因素，**个别场景可能出现负优化**。请在生产环境启用前先在测试机验证，并结合 `iperf3 / wrk / sockperf` 等工具建立你自己的基线。

测试环境：**2 核 / 4GB 内存 / 1Gbps 带宽 / Debian 12 VPS**（作者单次测试）

| 指标 | 优化前 | 优化后 | 变化 |
|:---|:---|:---|:---|
| TCP 吞吐量 | ~650 Mbps | ~920 Mbps | ↑ |
| 网络延迟 (RTT) | 45 ms | 38 ms | ↓ |
| 连接建立时间 | 3.2 ms | 1.8 ms | ↓ |
| 最大并发连接 | ~10,000 | >1,000,000 | ↑ |

---

<a id="compat"></a>

## 🖥️ 兼容性

### 支持的发行版

| 发行版 | 版本 | 状态 |
|:---|:---|:---|
| **Debian** | 10 / 11 / 12 | ✅ 完全支持 |
| **Ubuntu** | 20.04 / 22.04 / 24.04 | ✅ 完全支持 |
| **CentOS** | 7 / 8 / Stream | ✅ 完全支持 |
| **Rocky Linux** | 8 / 9 | ✅ 完全支持 |
| **AlmaLinux** | 8 / 9 | ✅ 完全支持 |
| **Arch Linux** | Rolling | ✅ 完全支持 |
| **openSUSE** | 15+ | ✅ 完全支持 |
| **Alpine Linux** | 3.12+ | ⚠️ 部分支持（无 systemd） |

### 内核要求

- **最低要求**：Linux 4.9+（BBR 支持）
- **推荐版本**：Linux 5.4+（更完善的网络栈）

### 虚拟化平台

| 平台 | 状态 |
|:---|:---|
| **KVM / QEMU** | ✅ 完全支持 |
| **VMware** | ✅ 完全支持 |
| **Xen** | ✅ 完全支持 |
| **Hyper-V** | ✅ 完全支持 |
| **OpenVZ** | ⚠️ 部分支持（无法修改内核参数） |
| **LXC / LXD** | ⚠️ 部分支持 |

---

## 快速上手

请使用 `root` 或具有 `sudo` 权限的用户执行：

```bash
/bin/bash <(wget -qO - https://raw.githubusercontent.com/longbertha/linux_setup/main/server-setup.sh)
````

**备用链接与国内加速:**

暂不支持国内服务器使用。

## 注意事项 ⚠️

  - **权限**: 脚本需要 `root` 权限才能进行系统级修改。
  - **安全操作**: 修改 SSH 端口、禁用密码登录等操作不可逆，请在操作前确保你已有新的、可靠的连接方式。
  - **反馈**: 如果你觉得这个脚本对你有帮助，或者发现了 Bug，欢迎来 [GitHub Issues](https://github.com/longbertha/linux_setup/issues) 给我提建议！


## 鸣谢
[https://github.com/SuperNG6/linux-setup.sh](https://github.com/SuperNG6/linux-setup.sh)
[https://github.com/buyi06/-Linux-](https://github.com/buyi06/-Linux-)