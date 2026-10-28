[English](README.md) | [中文](README_zh.md)

# CH32H417-sigrok-cli

An optimized sigrok-cli for the CH32H417 logic analyzer. Paired with sigrok-mcp-server, it talks to AI agents such as WorkBuddy over MCP, so you can **drive hardware capture, protocol decoding and timing analysis in natural language**.

## Hardware

A logic analyzer built on the CH32H417 dual-core RISC-V SoC — no FPGA involved.

- Up to 200 MHz digital sampling / 20 Msps analog sampling

- 16 digital + 2 analog channels, USB 3.0 high-speed transfer

Hardware project: LCEDA Open Source (立创开源)

## Key capabilities

1. Automatic device self-check — the AI recognizes the CH32H417 analyzer

2. Capture tasks issued in natural language, with automatic SPI/UART/I2C decoding

3. Automatic analysis of timing jitter and TX/RX latency, producing a fault report

## Requirements

### Hardware

- CH32H417 USB 3.0 logic analyzer

- USB 3.0 cable and the CH372 driver

### Software

- Windows

- The optimized sigrok-cli from this project + sigrok-mcp-server

- An MCP-capable AI agent (WorkBuddy)

## Quick start

1. Clone the repository and keep all files under a **pure-ASCII path** (no Chinese characters)

2. Install the CH372 driver and set the `SIGROK_CLI_PATH` environment variable

3. Paste the MCP config below into your AI client, then approve the server under "Custom connectors" (it does not start until trusted)

```json
{
  "mcpServers": {
    "sigrok": {
      "command": "%WORKSPACE%/sigrok-mcp-server.exe",
      "args": [],
      "env": {
        "SIGROK_CLI_PATH": "%WORKSPACE%/sigrok-cli/sigrok-cli.exe",
        "SIGROK_TIMEOUT_SECONDS": "600",
        "SIGROK_WORKING_DIR": "%WORKSPACE%/sigrok-cli/workdir"
      },
      "disabled": false
    }
  }
}
```

**Note**: replace `%WORKSPACE%` with your actual project root (ASCII only, no spaces, no Chinese characters). Any Windows drive path works.

`SIGROK_WORKING_DIR` is where the server writes temporary capture files; create it beforehand. `SIGROK_TIMEOUT_SECONDS` is the per-operation timeout — raise it for long captures.

No `PYTHONHOME` / `PYTHONPATH` / `SIGROKDECODE_DIR` needed: sigrok-cli derives them from its own directory at startup, so the portable Python and the decoders only have to sit next to the executable.

## Use cases

- Development debugging: unattended long-duration capture for intermittent communication faults

- Production-line automation: batch checks of bus timing and communication anomalies

- Extensible: I3C, SAI and more bus protocols planned

## Open source projects used

1. [sigrokproject/sigrok-cli](https://github.com/sigrokproject/sigrok-cli) (GPL-3.0)

2. [KenosInc/sigrok-mcp-server](https://github.com/KenosInc/sigrok-mcp-server) (MIT)

## More details

[https://blog.csdn.net/qq_43010294/article/details/163077606](https://blog.csdn.net/qq_43010294/article/details/163077606)
