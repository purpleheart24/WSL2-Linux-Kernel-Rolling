# WSL2 Kernel Performance Optimization Config
# Target: Intel Core Ultra 258V (Lunar Lake)
# Date: 2026-03-02

## CPU 优化选项

### 编译器优化
```bash
# 使用 -O3 优化
CONFIG_CC_OPTIMIZE_FOR_PERFORMANCE=y
# CONFIG_CC_OPTIMIZE_FOR_SIZE is not set

# 或者使用 -Ofast (更激进，可能影响稳定性)
# CC_OPTIMIZE_FOR_PERFORMANCE_O3=y
```

### CPU 架构优化
```bash
# 针对 Intel 处理器优化
CONFIG_MCORE2=y
CONFIG_MPENTIUM4=y
CONFIG_X86_64=y
CONFIG_X86_64_V3=y

# 禁用 5 级页表 (258V 不支持)
CONFIG_X86_5LEVEL=n

# 禁用 AVX-512 (258V 不支持)
CONFIG_AS_AVX512=n
CONFIG_X86_AVX512=n
# CONFIG_AVX512_* 全部禁用
```

### LTO 优化
```bash
# 启用 Thin LTO (推荐，编译速度快)
CONFIG_LTO_CLANG=y
CONFIG_LTO_CLANG_THIN=y
CONFIG_LTO=n  # 禁用完整 LTO (太慢)

# 或者使用 GCC LTO
# CONFIG_LTO_GCC=y
# CONFIG_LTO_GCC_THIN=y
```

## 性能调优

### 调度器优化
```bash
# 自愿抢占 (降低延迟)
CONFIG_PREEMPT_VOLUNTARY=y
# CONFIG_PREEMPT_NONE is not set
# CONFIG_PREEMPT is not set

# 更高定时器频率 (1000Hz)
CONFIG_HZ_1000=y
# CONFIG_HZ_500 is not set
# CONFIG_HZ_300 is not set
# CONFIG_HZ_250 is not set
# CONFIG_HZ_100 is not set
CONFIG_HZ=1000
```

### 内存优化
```bash
# 启用透明大页
CONFIG_TRANSPARENT_HUGEPAGE=y
CONFIG_TRANSPARENT_HUGEPAGE_MADVISE=y

# 启用 KSM (内核同页合并)
CONFIG_KSM=y
```

### I/O 优化
```bash
# 默认 I/O 调度器：BFQ (适合桌面)
CONFIG_BFQ_GROUP_IOSCHED=y
CONFIG_BFQ_IOSCHED=y

# 或者使用 MQ-DEADLINE (适合 SSD)
CONFIG_MQ_IOSCHED_DEADLINE=y
```

## 移除不需要的特性

### 禁用旧硬件支持
```bash
# 禁用旧的 MTRR 支持
CONFIG_MTRR_SANITIZER=y
CONFIG_MTRR_SANITIZER_ENABLE_DEFAULT=1
CONFIG_MTRR_SANITIZER_SPARE_REG_NR_DEFAULT=1

# 禁用 EISA 总线
CONFIG_EISA=n

# 禁用旧的 ISA DMA
CONFIG_DMADEV=n
```

### 精简驱动
```bash
# 只保留必要的驱动
# 移除不用的网卡、声卡、显卡驱动
# 保留 WSL2 必需的 dxgkrnl 驱动
CONFIG_DXGKRNL=y
CONFIG_HYPERV=y
CONFIG_HYPERV_BALLOON=y
CONFIG_HYPERV_STORAGE=y
CONFIG_HYPERV_NET=y
CONFIG_HYPERV_UTILS=y
```

## 编译选项

### GCC/Clang 标志
```bash
# 添加到 Makefile 或编译环境
CFLAGS="-O3 -march=alderlake -mtune=intel -flto=thin -fuse-ld=lld"
LDFLAGS="-O3 -flto=thin -fuse-ld=lld"

# 或者使用 -Ofast (更激进)
# CFLAGS="-Ofast -march=alderlake -mtune=intel -flto=thin"
```

### 并行编译
```bash
# 使用所有 CPU 核心
make -j$(nproc)

# 或者指定核心数
make -j16
```

## 测试计划

1. **基础测试**:
   - 编译成功
   - WSL2 正常启动
   - 基本功能正常

2. **性能测试**:
   - UnixBench 基准测试
   - 编译速度测试
   - I/O 性能测试

3. **稳定性测试**:
   - 长时间运行测试
   - 压力测试
   - 内存泄漏检测

## 预期效果

- **编译速度**: +10-20% (LTO 可能变慢)
- **运行时性能**: +5-15%
- **启动速度**: +5-10%
- **内存占用**: 可能略增

## 注意事项

⚠️ **LTO 会增加编译时间** (可能 2-3 倍)
⚠️ **-Ofast 可能影响浮点精度**
⚠️ **需要充分测试稳定性**
