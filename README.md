# CH32H417\-sigrok\-cli

适配 CH32H417 逻辑分析仪的优化版 sigrok\-cli，搭配 sigrok\-mcp\-server，通过 MCP 协议对接 WorkBuddy 等 AI，**自然语言操控硬件采集、解码、自动时序分析**。

## 硬件简介

基于CH32H417 双核 RISC\-V 单芯片打造的逻辑分析仪，无需 FPGA

- 最高 200MHz 数字采样 / 20Msps 模拟采样

- 16 路数字 \+ 2 路模拟通道，USB3\.0 高速传输
硬件开源工程：立创开源

## 核心能力

1. 设备自动自检，AI 识别 CH32H417 分析仪

2. 自然语言下发采集任务，SPI/UART/I2C 自动解码

3. 自动分析时序抖动、收发延迟，输出故障分析报告

## 环境依赖

### 硬件

- CH32H417 USB3\.0 逻辑分析仪

- USB3\.0 数据线、CH372 驱动

### 软件

- Windows 系统

- 本项目优化 sigrok\-cli \+ sigrok\-mcp\-server

- 支持 MCP 的 AI Agent（WorkBuddy）

## 快速部署

1. 克隆仓库，全部文件放**纯英文路径**

2. 安装 CH372 驱动，配置环境变量 `SIGROK_CLI_PATH`

3. AI 客户端填入下方 MCP 配置，启动服务即可使用

```json
{
  "sigrok": {
    "command": "%WORKSPACE%/sigrok-mcp-server.exe",
    "args": [],
    "env": {
      "SIGROK_CLI_PATH": "%WORKSPACE%/sigrok-cli/sigrok-cli.exe",
      "SIGROK_TIMEOUT_SECONDS": "600",
      "SIGROK_WORKING_DIR": "%WORKSPACE%/sigrok-cli",
      "PYTHONHOME": "%WORKSPACE%/sigrok-cli/python-portable",
      "PYTHONPATH": "%WORKSPACE%/sigrok-cli/python-portable/lib/python3.14",
      "SIGROKDECODE_DIR": "%WORKSPACE%/sigrok-cli/decoders",
      "PYTHONDONTWRITEBYTECODE": "1",
      "PATH": "%WORKSPACE%/sigrok-cli;${PATH}"
    },
    "disabled": false
  }
}
```

**配置说明**：将`%WORKSPACE%` 替换为你的项目实际根目录（文件夹纯英文、无空格、无中文），支持 Windows 任意磁盘路径，适配所有本地部署环境。

## 应用场景

- 研发调试：长时间无人挂测，捕捉偶发通信故障

- 产线自动化：批量自动检测总线时序、通信异常

- 可扩展：后续支持 I3C、SAI 等总线协议

## 引用开源项目

1. [sigrokproject/sigrok\-cli](https://github.com/sigrokproject/sigrok-cli) \(GPL\-3\.0\)

2. [KenosInc/sigrok\-mcp\-server](https://github.com/KenosInc/sigrok-mcp-server) \(MIT\)

## 详细介绍
[https://blog.csdn.net/qq_43010294/article/details/163077606](https://blog.csdn.net/qq_43010294/article/details/163077606)
