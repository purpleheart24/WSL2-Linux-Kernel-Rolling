# WSL2 启动问题修复指南

## 🔍 问题诊断

**错误**: `HCS_E_CONNECTION_TIMEOUT`  
**原因**: 自定义内核配置不完整，缺少必需的 WSL2 驱动

---

## ✅ 解决方案

### 方案 1: 使用完整配置文件 (推荐)

我们的 `config-wsl-258v-optimized` 太精简了，需要基于微软官方配置。

**步骤**:
1. 下载微软官方配置作为基础
2. 应用性能优化选项
3. 重新编译

### 方案 2: 临时使用默认内核

编辑 `%USERPROFILE%\.wslconfig`:
```ini
[wsl2]
# 注释掉自定义内核，使用默认
# kernel=C:\\path\\to\\bzImage
```

然后:
```powershell
wsl --shutdown
wsl
```

---

## 🔧 修复后的配置

需要确保以下必需选项启用:

```bash
# WSL2 必需
CONFIG_WSL=y
CONFIG_DXGKRNL=y
CONFIG_DXGKRNL_PCI=y
CONFIG_HYPERV=y
CONFIG_HYPERV_BALLOON=y
CONFIG_HYPERV_STORAGE=y
CONFIG_HYPERV_NET=y
CONFIG_HYPERV_UTILS=y
CONFIG_HYPERV_VSOCKETS=y

# 文件系统
CONFIG_EXT4_FS=y
CONFIG_EXT4_FS_POSIX_ACL=y
CONFIG_TMPFS=y
CONFIG_TMPFS_POSIX_ACL=y

# 网络
CONFIG_INET=y
CONFIG_NETFILTER=y
CONFIG_BRIDGE_NETFILTER=y

# 必需的内核特性
CONFIG_NAMESPACES=y
CONFIG_UTS_NS=y
CONFIG_IPC_NS=y
CONFIG_USER_NS=y
CONFIG_PID_NS=y
CONFIG_NET_NS=y
CONFIG_CGROUPS=y
CONFIG_CGROUP_FREEZER=y
CONFIG_CGROUP_DEVICE=y
CONFIG_CGROUP_CPUACCT=y
CONFIG_CGROUP_PIDS=y

# 虚拟化
CONFIG_KVM=y
CONFIG_KVM_INTEL=y
```

---

## 📋 下一步

1. **等待性能优化构建完成** - 使用完整配置重新编译
2. **测试新内核** - 确保所有 WSL2 功能正常
3. **性能对比** - 测试优化效果

---

**更新时间**: 2026-03-02 15:30 GMT+8
