# Zstandard (zstd) - Windows XP Compatibility Fork

## 关于此 Fork

**本项目是官方 [facebook/zstd](https://github.com/facebook/zstd) 仓库的一个分支，旨在为 zstd 的多线程功能添加 Windows XP 兼容性。**

由于官方在 [Issue #932](https://github.com/facebook/zstd/issues/932) 中表示不会为 Windows XP 提供官方兼容支持，因此创建了此 fork 以满足相关需求。

## 主要修改：Windows XP 多线程兼容性

此 fork 的核心改动在于使启用了多线程支持 (`ZSTD_MULTITHREAD`) 的 zstd 库能够在 Windows XP 系统上正确编译和运行。原版 zstd 在较新版本中依赖于 Windows Vista 及更高版本才提供的原生条件变量 (Condition Variables)，而 Windows XP 缺乏此原生支持。

本 fork 通过以下方式解决了兼容性问题：

*   **目标平台设定:** 在 `lib/common/threading.h` 中，为 Windows 平台强制设定 `WINVER` 和 `_WIN32_WINNT` 为 `0x0501`，以确保使用 XP 兼容的 API。
*   **模拟条件变量:** 在 `lib/common/threading.c` 中，利用 Windows XP 支持的标准同步原语——**互斥量 (Mutex)** 和 **事件 (Event)**——模拟实现了条件变量的等待 (wait)、信号 (signal) 和广播 (broadcast) 行为，并提供了与 Pthreads 兼容的接口。

通过这些修改，使用此 fork 构建的多线程 zstd 库理论上可以在 Windows XP SP2/SP3 环境下运行。

## 构建

构建步骤与原始 zstd 项目基本一致。请参考官方构建文档：
[https://github.com/facebook/zstd/tree/dev/build](https://github.com/facebook/zstd/tree/dev/build)

**针对 Windows XP 的特别说明:**
在 Windows 上构建时，请务必确保你的开发环境（编译器、链接器、Windows SDK）配置为面向 Windows XP。例如，在 Visual Studio 中选择兼容 XP 的平台工具集 (如 `v141_xp`)，或在使用 MinGW/Clang 时添加相应的目标平台编译选项。本 fork 中的代码修改解决了 API 级别的兼容性，但最终生成的可执行文件仍需依赖正确的工具链设置才能与 XP 运行时兼容。

## 许可证

此 fork 继承 zstd 的原始许可证。本项目在 BSD 和 GPLv2 双重许可下发布。详细信息请参阅 [LICENSE](LICENSE) 和 [COPYING](COPYING) 文件。

## 免责声明

Windows XP 是一个已停止生命周期 (End-of-Life) 的操作系统，微软已不再提供安全更新和技术支持。在 Windows XP 上运行任何软件（包括此修改版的 zstd）都存在固有的安全风险。

此 fork 按“原样”提供，旨在满足特定的遗留系统兼容性需求，使用者需自行承担所有风险。建议仅在确实无法升级操作系统的特殊情况下使用。