# WSL2 Linux Mainline RC 内核构建修改说明

## 修改概述

本项目已修改为支持在 GitHub Actions 中自动构建最新的 Linux Mainline RC 内核。

## 新增文件

### 1. `.github/workflows/build-rc.yml`
**功能**: Mainline RC 内核构建 workflow

**主要特性**:
- 自动从 kernel.org 获取最新 RC 版本号
- 支持手动指定 RC 版本（通过 workflow_dispatch）
- 从官方 torvalds/linux.git 仓库克隆源码
- 支持 x86、x86-rt、arm64、arm64-rt 四种架构
- 可选构建 ZFS 模块
- 自动生成 VHDX 模块包
- 生成 SHA-256 校验和
- 自动创建 GitHub Release（标记为预发布）

**触发方式**:
- 定时触发：每天 UTC 00:00
- 手动触发：GitHub Actions 页面手动运行

### 2. `.github/workflows/trigger-rc.yml`
**功能**: RC 版本检测和触发构建

**主要特性**:
- 自动检测最新 RC 版本
- 检查是否已构建过该版本（避免重复构建）
- 仅在新版本可用时触发构建
- 构建完成后发送通知

**触发方式**:
- 定时触发：每天 UTC 00:00
- 手动触发：可指定特定 RC 版本

## 修改的文件

### 1. `.github/workflows/build.yml`
**修改内容**:
- 添加新的可选输入参数：
  - `kernel_source`: 内核来源（stable/mainline/rc）
  - `kernel_repo`: 内核仓库 URL
  - `kernel_branch`: 内核分支或标签
- `build_zfs` 改为可选参数（默认 false）

### 2. `README.md`
**修改内容**:
- 更新标题，表明支持 Stable 和 Mainline RC 两种内核
- 添加两个构建轨道的说明：
  - Stable Kernel（生产环境推荐）
  - Mainline RC Kernel（仅用于测试/开发）
- 添加 RC 内核安装说明
- 添加 GitHub Actions 构建说明
- 添加新的链接和徽章

## 使用方法

### 自动构建
RC 内核会每天自动构建（UTC 00:00），无需手动干预。

### 手动构建特定版本
1. 进入 GitHub Actions 页面
2. 选择 "Trigger RC Kernel Build" workflow
3. 点击 "Run workflow"
4. 在 "Specific RC version to build" 中输入版本号（如 `6.8-rc5`）
5. 点击 "Run workflow" 按钮

### 本地构建 RC 内核
```bash
# 1. 克隆官方 mainline 内核
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git --depth 1 --branch v6.8-rc5 linux-rc

# 2. 复制 WSL2 配置文件
cp arch/x86/configs/config-wsl-x86 linux-rc/arch/x86/configs/

# 3. 编译内核
cd linux-rc
make KCONFIG_CONFIG=arch/x86/configs/config-wsl-x86 -j$(nproc + 1)

# 4. 输出内核镜像
cp arch/x86/boot/bzImage ../bzImage-x86_64
```

## 版本命名规则

### Stable Kernel
- 标签格式：`v6.x.y`
- 内核版本：`6.x.y-WSL2-STABLE+`

### Mainline RC Kernel
- 标签格式：`rc-6.x-rcN`
- 内核版本：`6.x-rcN-WSL2-RC+`

## 输出文件

每次构建会生成以下文件：

| 文件名 | 说明 |
|--------|------|
| `bzImage-x86_64` | x86_64 架构内核镜像 |
| `bzImage-x86_64-rt` | x86_64 RT 内核镜像 |
| `Image-arm64` | ARM64 架构内核镜像 |
| `Image-arm64-rt` | ARM64 RT 内核镜像 |
| `*-addon.vhdx` | 对应的内核模块包 |

## 注意事项

### ⚠️ RC 内核警告
- RC 内核是**预发布版本**，可能包含 bug
- **不推荐**在生产环境中使用
- 仅用于测试新功能和开发
- 可能不稳定，请谨慎使用

### 构建时间
- 完整构建（4 架构）约需 60-90 分钟
- 启用 ZFS 构建会额外增加 10-15 分钟

### GitHub Actions 限制
- 需要 `CUSTOM_GITHUB_TOKEN` secret 来自动创建 Release
- 容器需要 `--privileged` 权限来构建 VHDX

## 后续改进建议

1. **缓存优化**: 添加内核源码缓存，加快构建速度
2. **增量构建**: 支持仅构建变化的架构
3. **测试套件**: 添加基本的内核启动测试
4. **通知集成**: 添加 Discord/Telegram 构建完成通知
5. **文档完善**: 创建详细的 Wiki 页面

## 相关资源

- [Linux Mainline Git](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git)
- [Linux Stable Git](https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git)
- [Kernel.org](https://www.kernel.org/)
- [WSL2 Kernel Source](https://github.com/microsoft/WSL2-Linux-Kernel)

---

**修改日期**: 2026-03-02  
**修改者**: OpenClaw Assistant
