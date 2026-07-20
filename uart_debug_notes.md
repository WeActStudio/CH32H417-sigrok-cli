# UART 解码问题分析

## 问题描述

使用 `sigrok-cli` 对 CH32H417 设备进行 UART 解码时，程序卡住或没有输出。

## 已确认的问题

### 1. session.c 没有在样本限制达到时停止 session

**修复**：在 `session.c` 中添加了 `sr_session_stop()` 调用：
- 在收到数据后检查样本限制
- 在处理数据前检查是否已达到限制

### 2. 缺少 SR_DF_END 调试日志

**修复**：将 `g_debug` 改为 `g_message` 以便在默认日志级别看到 SR_DF_END 消息。

## 待确认的问题

### 1. devc->limit_samples 是否正确设置

需要确认 CH32H417 驱动的 `devc->limit_samples` 是否被正确设置。

### 2. 读取线程是否正确检测样本限制

读取线程在 `protocol.c:803` 检查 `devc->sent_samples >= devc->limit_samples`，需要确认：
- `limit_samples` 是否正确设置
- `sent_samples` 是否正确累计

## 编译说明

**重要**：必须使用 MSYS2 MINGW64 环境编译：
```bash
D:\MSYS2\mingw64.exe -defterm -here -no-start -lc "./build.sh"
```

## 下一步

1. 重新编译 sigrok-cli
2. 测试 UART 解码，添加更详细的日志确认：
   - `limit_samples` 是否正确设置
   - `sent_samples` 是否正确累计
   - SR_DF_END 是否发送
   - EOF 是否发送到解码器
