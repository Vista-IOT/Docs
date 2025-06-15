---
layout: home
title: Welcome to Vista IoT Documentation
---

<div align="center">
  <img src="assets/images/logo.jpeg" alt="Vista IoT Logo" width="200" style="margin: 20px 0;"/>
</div>

# 🚀 Vista IoT - Industrial Gateway Platform

A comprehensive, self-contained **Industrial IoT Gateway** solution for Radxa Single Board Computers. This platform provides a complete alternative to commercial solutions like **Advantech Edge Link Studio**, with a web-based configuration interface, multi-protocol support, and system-level automation.

<div align="center">
  <img src="assets/images/square banner.jpeg" alt="Vista IoT Banner" width="600" style="margin: 20px 0;"/>
</div>

### ✨ Key Features

- 🖥️ **Web-based Configuration Dashboard** - Similar to Advantech Edge Link Studio
- ⚙️ **YAML-driven Configuration** - Generate and apply system configurations via web forms
- 🔄 **Automatic System Configuration** - Network, protocols, and services configured automatically
- 📡 **Multi-Protocol Support** - MQTT, Modbus TCP/RTU, OPC-UA, IEC 61850, DNP3
- 🏠 **Self-Contained** - No external dependencies, runs entirely on the Radxa SBC
- 🔐 **Industrial Grade** - Firewall, logging, backup, and security features

### 🎯 Perfect For

- **Industrial Automation** - Connect PLCs, RTUs, and SCADA systems
- **Factory Floor Integration** - Bridge legacy equipment with modern IoT platforms
- **Power System Monitoring** - IEC 61850 compliant substation automation
- **Remote Asset Management** - Monitor and control distributed industrial equipment
- **Edge Computing** - Process data locally before cloud transmission

### 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Web Dashboard (Port 8080)               │
└─────────────────────┬───────────────────────────────────────┘
                      │ REST API / WebSocket
                      │
┌─────────────────────▼───────────────────────────────────────┐
│                Configuration Engine                         │
│              (YAML Processor)                              │
└─────┬───────────────┬───────────────┬───────────────────────┘
      │               │               │
      ▼               ▼               ▼
┌───────────┐  ┌─────────────┐  ┌──────────────┐
│  Network  │  │  Protocols  │  │   System     │
│  Config   │  │   Config    │  │   Config     │
└───────────┘  └─────────────┘  └──────────────┘
      │               │               │
      ▼               ▼               ▼
┌───────────┐  ┌─────────────┐  ┌──────────────┐
│ Ethernet  │  │    MQTT     │  │   Firewall   │
│   WiFi    │  │   Modbus    │  │   Services   │
│   DNS     │  │   OPC-UA    │  │    Users     │
└───────────┘  └─────────────┘  └──────────────┘
```

> **Ready to get started?** Check out our [Quick Start Guide](getting-started) to deploy your gateway in minutes!

