# Rolling Release Kernel for WSL2 (Stable & Mainline RC)

[![Stable Kernel CI](https://img.shields.io/github/actions/workflow/status/Nevuly/WSL2-Linux-Kernel-Rolling/trigger.yml?label=Stable%20Build&logo=github-actions&logoColor=%23FFFFFF&style=for-the-badge&labelColor=%23000000)](https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/actions/workflows/trigger.yml)
[![RC Kernel CI](https://img.shields.io/github/actions/workflow/status/Nevuly/WSL2-Linux-Kernel-Rolling/trigger-rc.yml?label=RC%20Build&logo=github-actions&logoColor=%23FFFFFF&style=for-the-badge&labelColor=%23000000)](https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/actions/workflows/trigger-rc.yml)
[![Release](https://img.shields.io/github/v/release/Nevuly/WSL2-Linux-Kernel-Rolling?display_name=tag&label=STABLE&logo=Linux&logoColor=%23FFFFFF&style=for-the-badge&labelColor=%23000000)](https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/releases/latest)
[![RC Release](https://img.shields.io/github/v/release/Nevuly/WSL2-Linux-Kernel-Rolling?display_name=tag&label=RC&logo=Linux&logoColor=%23FFFFFF&style=for-the-badge&labelColor=%23ff9800&prefix=rc-)](https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/releases?q=tag%3Arc-)

Maintainer: Yang Jeong Hun (Nevuly)

## Introduction
The [WSL2-Linux-Kernel-Rolling][wsl2-kernel-rolling] repo contains the latest kernel source code and
configuration files for the [WSL2][about-wsl2].

### 📦 Two Build Tracks

#### 1. **Stable Kernel** (Recommended for Production)
- Built from the latest [Linux Stable Kernel][kernel-stable]
- Automatically built via [GitHub Actions][gh-actions] CI
- **Tested and stable** for daily use
- Release tag format: `v6.x.y`

#### 2. **Mainline RC Kernel** (For Testing/Development)
- Built from the latest [Linux Mainline Release Candidate][kernel-mainline]
- Automatically built daily via [GitHub Actions][gh-actions] CI
- ⚠️ **May contain bugs and instability** - NOT for production use
- For testing new features and development only
- Release tag format: `rc-6.x-rcN`

This kernel is automatically built via [Github Actions][gh-actions] CI whenever a [newer stable kernel is released][kernel-stable] or [new RC version is available][kernel-mainline].

## Install Instructions

### Stable Kernel
Please see this [wiki][wiki] for stable kernel installation.

### Mainline RC Kernel (Testing Only)
⚠️ **Warning**: RC kernels are pre-release and may be unstable!

1. Download the latest RC kernel from [Releases - RC](https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/releases?q=tag%3Arc-)
2. Extract the kernel files
3. Configure WSL2 to use the custom kernel (see [wiki][wiki])
4. **Note**: RC kernel version format is `6.x-rcN-WSL2-RC+`

## Reporting Bugs
If you discover an issue relating to WSL or the WSL2 kernel, please report it on
the [Issues tab][issue].

## Feature Requests
If you want to fix a bug or add new features, Please use the [Pull Request][pr].

## Build Instructions

### Local Build (Stable Kernel)
Instructions for building WSL2 kernel with an Arch Linux distribution are
as follows:

1. Install the build dependencies (Arch Linux):  
   `$ sudo pacman -S aarch64-linux-gnu-gcc bc bison curl flex gcc git pahole python unzip wget zip`

2. Build the kernel using the WSL2 kernel configuration (x86):  
   `$ make KCONFIG_CONFIG=arch/x86/configs/config-wsl-x86`

3. Build the kernel using the WSL2 kernel configuration (arm64):  
   `$ export ARCH=arm64 && export CROSS_COMPILE=aarch64-linux-gnu-`  
   `$ make KCONFIG_CONFIG=arch/arm64/configs/config-wsl-arm64`

4. Save the module to a separate folder (Optional. Only for local build):  
   `$ sudo make modules_install`

### GitHub Actions Build

#### Stable Kernel
The stable kernel is automatically built when a new stable kernel version is released.

#### Mainline RC Kernel
The RC kernel is automatically built daily at UTC 00:00. You can also manually trigger a build:

1. Go to [Actions - Trigger RC Kernel Build](https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/actions/workflows/trigger-rc.yml)
2. Click "Run workflow"
3. Optionally specify a specific RC version (e.g., `6.8-rc5`)
4. Click "Run workflow" button

The build will produce:
- Kernel image (`bzImage-x86_64`, `Image-arm64`, etc.)
- Kernel modules addon package (`*-addon.vhdx`)
- SHA-256 checksums for verification

#### Build Configuration Files

- **Stable Kernel**: `.github/workflows/build.yml`
- **RC Kernel**: `.github/workflows/build-rc.yml`
- **RC Trigger**: `.github/workflows/trigger-rc.yml`

## Credits
 * The Linux community who created a awesome kernel.
 * Microsoft which produced WSL2 and dxgkrnl patches.

## Links
 * [Stable Kernel Source][kernel-stable]
 * [Mainline RC Kernel Source][kernel-mainline]
 * [Installation Wiki][wiki]
 * [Issue Tracker][issue]

[wsl2-kernel-rolling]: https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling
[about-wsl2]: https://docs.microsoft.com/en-us/windows/wsl/about#what-is-wsl-2
[gh-actions]: https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/actions
[kernel-stable]: https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
[kernel-mainline]: https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
[wiki]: https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/wiki/Install-Instructions
[issue]: https://github.com/Nevuly/WSL2-Rolling-Kernel-Issue/issues
[pr]: https://github.com/Nevuly/WSL2-Linux-Kernel-Rolling/pulls
