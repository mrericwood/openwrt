# OpenWrt x86 VMDK Firmware Build Workflow

## 🎯 项目简介

本仓库使用 **GitHub Actions** 在云端自动编译 **OpenWrt 25.12** 官方稳定版固件，运行架构为 **x86_64**，输出镜像格式为 **VMDK**（适用于虚拟化环境如 VMware、VirtualBox、Proxmox VE）。

---

## ✨ 主要特点

- ✅ **自动编译**: 推送到 gh-actions-workflow 分支或每周日凌晨 3 点自动触发
- ✅ **完整功能**: 包含 LuCI 界面、Argon 主题、Passwall/OpenClash 等第三方插件
- ✅ **虚拟化友好**: 输出 VMDK 镜像格式，可直接用于虚拟机环境
- ✅ **自定义配置**: 预设 DHCP/静态 IP 网络配置，开箱即用
- ✅ **多系统支持**: x86/x86_64/x86_64_generic 三种目标架构

---

## 📦 内置组件

### OpenWrt 官方软件包
- luci - Web 管理界面基础
- luci-base - LuCI 基础功能
- luci-i18n-base-zh-cn - 简体中文语言包
- lua - Lua 脚本引擎

### 第三方插件
| 插件名称 | 描述 | GitHub 仓库 |
|---------|------|------------|
| **Argon 主题** | 现代化深色主题界面 | [@jerrykuku/luci-theme-argon](https://github.com/jerrykuku/luci-theme-argon) |
| **OpenClash** | 基于 Clash 的代理插件 | [@vernesong/OpenClash](https://github.com/vernesong/OpenClash) |
| **Passwall** | Passwall 代理工具 | [@Openwrt-Passwall/openwrt-passwall](https://github.com/Openwrt-Passwall/openwrt-passwall) |
| **TTYD** | Web 远程终端管理 | - |
| **Tailscale** | 组网工具 | - |
| **SQM** | 流量优化工具 | - |

---

## 🚀 使用方法

### 方式一：手动触发编译

1. 向 gh-actions-workflow 分支推送任何更改（例如创建 commit）
2. GitHub Actions 会自动触发编译流程
3. 编译完成后，在仓库的 **Actions** 页面查看下载 Artifacts

### 方式二：定时自动编译

默认每周日凌晨 3 点自动编译一次。

### 下载固件镜像

编译完成后，可以在以下位置下载镜像：
- **Actions 页面**: https://github.com/mrericwood/openwrt/actions
- **Artifacts**: 点击编译任务 → Artifacts → 下载 .vmdk 文件

---

## 🏗️ Workflow 说明

### 构建流程步骤

| 步骤 | 描述 |
|------|------|
| **Checkout Repository** | 克隆仓库代码到 runner |
| **Install Dependencies** | 安装 Ubuntu runner 所需依赖包 |
| **Clone OpenWrt Source** | 克隆 OpenWrt 25.12 官方源码（浅克隆） |
| **Clone Third-Party Plugins** | 克隆第三方插件到 package/ 目录 |
| **Update and Install Feeds** | 更新和安装 feeds |
| **Configure** | 生成 .config 配置，设置 x86/VMDK/packages |
| **Customize Network** | 配置网络（DHCP/静态 IP） |
| **Build Firmware** | 编译固件（make download → make） |
| **Upload Artifacts** | 上传 VMDK/IMG/TAR.GZ 镜像 |

### Workflow 文件位置

`
.github/workflows/openwrt-build.yml
`

---

## 📁 目录结构

`
openwrt/
├── .github/
│   └── workflows/
│       ├── openwrt-build.yml    # GitHub Actions workflow
│       └── README.md            # 本说明文档
├── README.md                    # （可选）项目 README
└── .git/                        # Git 仓库数据（忽略）
`

---

## 🔧 配置说明

### .config 关键配置

`makefile
CONFIG_TARGET_x86=y
CONFIG_TARGET_x86_64=y
CONFIG_TARGET_x86_64_generic=y

CONFIG_TARGET_IMAGES_GZIP=y
CONFIG_TARGET_IMAGES_VMDK=y

CONFIG_PACKAGE_luci-theme-argon=y
CONFIG_PACKAGE_luci-app-passwall=y
CONFIG_PACKAGE_luci-app-openclash=y
CONFIG_PACKAGE_luci-app-ttyd=y
CONFIG_PACKAGE_tailscale=y
CONFIG_PACKAGE_luci-app-sqm=y
`

---

## ⚙️ Runner 环境

- **系统**: Ubuntu Latest
- **Shell**: Bash
- **Make Jobs**: -j16（自动使用所有 CPU 核心）
- **缓存**: 未启用（首次编译时间约 30-60 分钟，取决于网络速度）

---

## 📝 版本说明

### OpenWrt 版本
- **当前版本**: OpenWrt 25.12
- **源码仓库**: https://git.openwrt.org/openwrt/openwrt.git
- **分支**: 25.12.0

### 支持的 VMDK 格式

编译后生成的 VMDK 文件支持：
- ✅ VMware Workstation/Player
- ✅ VirtualBox (需转换)
- ✅ Proxmox VE
- ✅ QEMU/KVM

---

## 🔗 相关链接

- [OpenWrt 官方源码仓库](https://git.openwrt.org/openwrt/openwrt.git)
- [Argon 主题](https://github.com/jerrykuku/luci-theme-argon)
- [OpenClash](https://github.com/vernesong/OpenClash)
- [Passwall](https://github.com/Openwrt-Passwall/openwrt-passwall)

---

## ⚠️ 注意事项

1. **首次编译**: 约需 30-60 分钟（取决于网络速度和 runner 负载）
2. **存储空间**: Ubuntu runner 默认提供充足空间
3. **镜像验证**: 建议下载后使用校验工具验证 VMDK 完整性
4. **网络配置**: 预设 DHCP，也可在首次启动时通过 LuCI 修改为静态 IP

---

## 📜 License

本固件基于 [GPL-2.0-or-later](https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) 开源协议分发。第三方插件遵循其各自的许可证。

---

**作者**: mrericwood  
**最后更新**: 2026-11-24  

---

## 💡 常见问题

### Q: 如何加速编译？
A: GitHub Actions 默认使用免费 runner，编译速度取决于队列。可考虑：
- 使用 GITHUB_TOKEN 提升优先级
- 手动触发更频繁

### Q: VMDK 文件在哪里下载？
A: 进入 Actions 页面 → 选择最近一次运行的 Workflow → Artifacts → 下载 VMDK 文件

### Q: 如何自定义配置？
A: 提交 commit 到 gh-actions-workflow 分支即可触发新的编译，无需修改 workflow 本身。

---

如有问题，欢迎在 [GitHub Issues](https://github.com/mrericwood/openwrt/issues) 中提出！
