---
layout: page
title: Architecture
permalink: /architecture/
---

# Vista IoT Gateway Architecture

Comprehensive overview of the Vista IoT Gateway platform architecture, designed as a self-contained industrial IoT solution.

<div align="center">
  <img src="assets/images/logo.jpeg" alt="Vista IoT Logo" width="150" style="margin: 20px 0;"/>
</div>

## 🏗️ System Overview

The Radxa IoT Gateway is engineered as a **modular, industrial-grade IoT platform** that runs entirely on Radxa Single Board Computers. It provides a complete alternative to commercial solutions like Advantech Edge Link Studio.

### 🎯 Design Principles

- **Self-Contained**: No external cloud dependencies required
- **Industrial Grade**: Built for 24/7 operation in harsh environments
- **Protocol Agnostic**: Support for multiple industrial communication protocols
- **Web-First**: Complete configuration through web interface
- **Modular**: Easy to extend with additional protocols and features

## 🖥️ Core Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Web Dashboard (Next.js)                        │
│                     http://radxa-ip:8080                             │
└─────────────────────────────────────┬─────────────────────────────────────────┘
                                 │ REST API / WebSocket
                                 │
┌─────────────────────────────────────▼─────────────────────────────────────────┐
│                          Flask API Server                            │
│                        (app.py - Port 8080)                         │
└─────────────────────────────────────┬─────────────────────────────────────────┘
                                 │ YAML Configuration
                                 │
┌─────────────────────────────────────▼─────────────────────────────────────────┐
│                   Configuration Engine                             │
│                  (gateway_configurator.py)                        │
└──────────┬───────────────────┬───────────────────┬───────────────────┘
          │                   │                   │
          ▼                   ▼                   ▼
┌──────────┐   ┌───────────────────┐   ┌───────────────────┐
│ Network  │   │   Protocol Stack     │   │   System Stack     │
│ Manager  │   │                   │   │                   │
└──────────┘   └───────────────────┘   └───────────────────┘
          │                   │                   │
          ▼                   ▼                   ▼
 ┌─────────┐              ┌────────────┐      ┌────────────┐
 │Ethernet │              │MQTT:1883    │      │Hostname    │
 │WiFi     │              │Modbus:502   │      │Firewall    │
 │Firewall │              │OPC-UA:4840  │      │NTP Sync    │
 │DNS      │              │DNP3:20000   │      │Logging     │
 └─────────┘              │IEC61850     │      │Services    │
                          └────────────┘      └────────────┘
```

## 🔧 Core Components

### 1. Web Dashboard (Frontend)

**Technology Stack:**
- **Next.js 14**: React framework with server-side rendering
- **TypeScript**: Type-safe development
- **Tailwind CSS**: Utility-first CSS framework
- **Radix UI**: Accessible component primitives
- **Zustand**: Lightweight state management
- **React Hook Form + Zod**: Form handling and validation

**Key Features:**
- Real-time system monitoring
- Protocol configuration interfaces
- Network settings management
- YAML configuration editor with Monaco
- Responsive design for mobile/tablet access

### 2. Flask API Server (Backend)

**Core Services:**
- **Configuration API**: CRUD operations for gateway settings
- **WebSocket Server**: Real-time updates and monitoring
- **Static File Serving**: Hosts the built Next.js application
- **Authentication**: User session management

**File Structure:**
```
/opt/radxa-gateway/
├── app.py                    # Main Flask application
├── config/
│   ├── gateway.yaml           # Main configuration
│   ├── modbus.json           # Modbus-specific config
│   └── opcua.json            # OPC-UA-specific config
├── web-dashboard/out/         # Built frontend
├── logs/                     # Application logs
└── data/                     # Runtime data
```

### 3. Configuration Engine

**Purpose:** Translates YAML configuration into system-level changes

**Capabilities:**
- **Network Configuration**: Ethernet, WiFi, static/DHCP
- **Service Management**: Systemd service creation and management
- **Protocol Setup**: Configure protocol-specific settings
- **Firewall Rules**: UFW configuration
- **System Settings**: Hostname, timezone, NTP

**Configuration Flow:**
```
Web Form → YAML Generation → Validation → System Application → Service Restart
```

## 📊 Protocol Stack

### Supported Industrial Protocols

| Protocol | Port | Purpose | Implementation |
|----------|------|---------|----------------|
| **MQTT** | 1883 | IoT Messaging | Mosquitto Broker + Paho Client |
| **Modbus TCP** | 502 | PLC Communication | PyModbus |
| **Modbus RTU** | Serial | Serial PLC Communication | PyModbus + PySerial |
| **OPC-UA** | 4840 | Industrial Automation | python-opcua |
| **DNP3** | 20000 | SCADA Systems | dnp3-python |
| **IEC 61850** | 102 | Power System Communication | libIEC61850 |

### Protocol Architecture

```
┌───────────────────────────────────────────────────────────────────────────────┐
│                        Field Devices                                │
│    PLCs    │   RTUs    │   IEDs    │  Sensors  │   HMIs   │
└────────────┬───────────┬───────────┬──────────┬───────────┘
            │           │           │          │
       Modbus      OPC-UA     IEC61850     DNP3
            │           │           │          │
            ▼           ▼           ▼          ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                    Radxa IoT Gateway                               │
│              Protocol Conversion & Data Aggregation                 │
└───────────────────────────────────────────────────────────────────────────────┘
                                 │
                              MQTT
                                 │
                                 ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                   Cloud Platforms                                │
│        AWS IoT   │   Azure IoT   │   Google IoT   │   Private Cloud   │
└───────────────────────────────────────────────────────────────────────────────┘
```

## 📊 Data Flow Architecture

### Real-time Data Processing

```
1. Device Polling
   │
   ▼
2. Protocol Translation
   │
   ▼  
3. Data Normalization
   │
   ▼
4. Local Storage
   │
   ▼
5. MQTT Publishing
   │
   ▼
6. Cloud Transmission
```

### Configuration Data Flow

```
Web Form Input → Form Validation → YAML Generation → Config Engine → System Changes
     │                │                │               │              │
     ▼                ▼                ▼               ▼              ▼
Field Types     Zod Schema      gateway.yaml  Configurator   Network/Services
```

## 🔒 Security Architecture

### Network Security

- **UFW Firewall**: Configurable rules for protocol ports
- **VPN Support**: IPSec/OpenVPN for secure remote access
- **Network Segmentation**: Separate VLANs for field and enterprise networks

### Application Security

- **Authentication**: Optional web dashboard authentication
- **HTTPS/TLS**: SSL certificate support for web interface
- **Protocol Security**: TLS support for MQTT, OPC-UA security modes

### System Security

- **File Permissions**: Restricted access to configuration files
- **Service Isolation**: Systemd service sandboxing
- **Backup/Recovery**: Automated configuration backups

## 🚀 Performance Characteristics

### Hardware Requirements

| Component | Minimum | Recommended | Production |
|-----------|---------|-------------|------------|
| **RAM** | 2GB | 4GB | 8GB |
| **Storage** | 8GB | 32GB | 64GB |
| **Network** | 100Mbps | 1Gbps | 1Gbps+ |
| **CPU** | Quad-core ARM | 6+ cores | 8+ cores |

### Performance Targets

| Metric | Target | Measured (ROCK 5B) |
|--------|--------|-----------------|
| **Concurrent Modbus Devices** | 100+ | 120 |
| **MQTT Messages/sec** | 1000+ | 850 |
| **OPC-UA Nodes** | 10,000+ | 8,500 |
| **Web Response Time** | <100ms | 85ms |
| **Boot to Operational** | <30s | 28s |

## 🔧 Extensibility

### Adding New Protocols

1. **Protocol Handler**: Implement in `/protocols/new_protocol.py`
2. **Configuration Schema**: Add to YAML schema
3. **Web Interface**: Create configuration forms
4. **Documentation**: Update protocol guides

### Custom Web Pages

1. **Frontend**: Add Next.js pages in `/web-dashboard/app/`
2. **API Endpoints**: Extend Flask routes in `app.py`
3. **State Management**: Extend Zustand stores

### Hardware Integration

- **GPIO Support**: Direct hardware control
- **Serial Interfaces**: RS-232/485 support
- **Industrial I/O**: Digital/analog I/O expansion
- **Cellular/LoRaWAN**: Wireless connectivity modules


## 🔄 System Flow Diagrams

### Overall System Architecture

```mermaid
graph TB
    subgraph "Field Level"
        PLC[PLC/RTU]
        IED[IED]
        Sensor[Sensors]
        HMI[HMI]
    end
    
    subgraph "Vista IoT Gateway"
        WebUI[Web Dashboard<br/>Next.js]
        API[Flask API Server]
        ConfigEngine[Configuration Engine]
        
        subgraph "Protocol Stack"
            MQTT[MQTT Broker]
            Modbus[Modbus Handler]
            OPCUA[OPC-UA Server]
            DNP3[DNP3 Handler]
            IEC[IEC61850 Handler]
        end
        
        subgraph "System Services"
            Network[Network Manager]
            Security[Security Manager]
            Storage[Data Storage]
        end
    end
    
    subgraph "Cloud/Enterprise"
        AWS[AWS IoT]
        Azure[Azure IoT]
        Private[Private Cloud]
        SCADA[SCADA System]
    end
    
    PLC -->|Modbus TCP| Modbus
    IED -->|IEC61850| IEC
    Sensor -->|Custom Protocol| API
    HMI -->|OPC-UA| OPCUA
    
    WebUI --> API
    API --> ConfigEngine
    ConfigEngine --> Network
    ConfigEngine --> Security
    
    MQTT --> AWS
    MQTT --> Azure
    MQTT --> Private
    OPCUA --> SCADA
    
    Modbus --> MQTT
    OPCUA --> MQTT
    DNP3 --> MQTT
    IEC --> MQTT
```

### Data Flow Sequence

```mermaid
sequenceDiagram
    participant Device as Industrial Device
    participant Protocol as Protocol Handler
    participant Engine as Data Engine
    participant MQTT as MQTT Broker
    participant Cloud as Cloud Platform
    participant Dashboard as Web Dashboard
    
    Device->>Protocol: Raw Data (Modbus/OPC-UA)
    Protocol->>Engine: Parsed Data
    Engine->>Engine: Data Validation
    Engine->>Engine: Data Transformation
    Engine->>MQTT: Normalized JSON
    MQTT->>Cloud: Telemetry Data
    MQTT->>Dashboard: Real-time Updates
    
    Dashboard->>Engine: Configuration Change
    Engine->>Protocol: Update Settings
    Protocol->>Device: Apply Configuration
```

### Web Dashboard Application Flow

```mermaid
flowchart TD
    A[User Access] --> B{Authentication}
    B -->|Success| C[Dashboard Home]
    B -->|Fail| D[Login Page]
    D --> B
    
    C --> E[Overview Tab]
    C --> F[Network Tab]
    C --> G[Protocols Tab]
    C --> H[Security Tab]
    C --> I[Logs Tab]
    
    G --> G1[MQTT Config]
    G --> G2[Modbus Config]
    G --> G3[OPC-UA Config]
    G --> G4[DNP3 Config]
    
    G1 --> J[Save Configuration]
    G2 --> J
    G3 --> J
    G4 --> J
    
    J --> K[Validate YAML]
    K -->|Valid| L[Apply to System]
    K -->|Invalid| M[Show Errors]
    M --> G
    
    L --> N[Restart Services]
    N --> O[System Updated]
```

### Protocol Communication Pattern

```mermaid
graph LR
    subgraph "Modbus Network"
        M1[PLC 1<br/>192.168.1.10]
        M2[PLC 2<br/>192.168.1.11]
        M3[RTU 1<br/>192.168.1.12]
    end
    
    subgraph "OPC-UA Network"
        O1[Server 1<br/>opc.tcp://192.168.1.20:4840]
        O2[Server 2<br/>opc.tcp://192.168.1.21:4840]
    end
    
    subgraph "Vista IoT Gateway"
        Gateway[Gateway<br/>192.168.1.100]
        
        subgraph "Internal Services"
            MB[Modbus Client]
            OPC[OPC-UA Client]
            MQTT_B[MQTT Broker:1883]
        end
    end
    
    subgraph "MQTT Clients"
        C1[Cloud Service]
        C2[Mobile App]
        C3[Analytics Engine]
    end
    
    M1 -->|TCP:502| MB
    M2 -->|TCP:502| MB
    M3 -->|TCP:502| MB
    
    O1 -->|OPC-UA| OPC
    O2 -->|OPC-UA| OPC
    
    MB --> MQTT_B
    OPC --> MQTT_B
    
    MQTT_B --> C1
    MQTT_B --> C2
    MQTT_B --> C3
```

### Network Topology

```mermaid
graph TB
    subgraph "Enterprise Network (VLAN 10)"
        Internet[Internet]
        Router[Enterprise Router]
        Switch1[Management Switch]
        PC[Admin PC]
    end
    
    subgraph "Industrial Network (VLAN 20)"
        Switch2[Industrial Switch]
        Gateway[Vista IoT Gateway<br/>192.168.20.100]
        
        subgraph "Field Devices"
            PLC1[PLC 1<br/>192.168.20.10]
            PLC2[PLC 2<br/>192.168.20.11]
            HMI1[HMI<br/>192.168.20.15]
            IED1[IED<br/>192.168.20.20]
        end
    end
    
    subgraph "DMZ (VLAN 30)"
        Firewall[Firewall]
        VPN[VPN Server]
    end
    
    Internet --> Router
    Router --> Switch1
    Router --> Firewall
    Switch1 --> PC
    
    Firewall --> Switch2
    Switch2 --> Gateway
    Switch2 --> PLC1
    Switch2 --> PLC2
    Switch2 --> HMI1
    Switch2 --> IED1
    
    Firewall --> VPN
    
    style Gateway fill:#f9f,stroke:#333,stroke-width:4px
    style Firewall fill:#f96,stroke:#333,stroke-width:2px
```

### Configuration Management Workflow

```mermaid
flowchart TD
    A[Start Configuration] --> B[Load Current Config]
    B --> C[Display Web Form]
    C --> D[User Input]
    D --> E[Form Validation]
    
    E -->|Invalid| F[Show Validation Errors]
    F --> D
    
    E -->|Valid| G[Generate YAML]
    G --> H[Schema Validation]
    
    H -->|Invalid| I[Show Schema Errors]
    I --> D
    
    H -->|Valid| J[Create Backup]
    J --> K[Apply Network Config]
    K --> L[Apply Protocol Config]
    L --> M[Apply System Config]
    
    M --> N[Update Firewall]
    N --> O[Restart Services]
    O --> P[Verify Configuration]
    
    P -->|Success| Q[Configuration Complete]
    P -->|Failure| R[Rollback Configuration]
    R --> S[Restore from Backup]
    S --> T[Show Error Message]
    T --> D
```

### Security Architecture

```mermaid
graph TB
    subgraph "External Threats"
        Hacker[External Attacker]
        Malware[Malware]
    end
    
    subgraph "Network Security"
        Firewall[UFW Firewall]
        VPN[VPN Access]
        VLAN[VLAN Segmentation]
    end
    
    subgraph "Vista IoT Gateway"
        WebAuth[Web Authentication]
        TLS[TLS/SSL Encryption]
        FilePerms[File Permissions]
        
        subgraph "Protocol Security"
            MQTTAuth[MQTT Authentication]
            OPCUASec[OPC-UA Security]
            ModbusSec[Modbus Security]
        end
        
        subgraph "System Security"
            ServiceIso[Service Isolation]
            LogMon[Log Monitoring]
            BackupSys[Backup System]
        end
    end
    
    subgraph "Internal Network"
        AdminPC[Admin PC]
        FieldDevices[Field Devices]
    end
    
    Hacker -.->|Blocked| Firewall
    Malware -.->|Blocked| Firewall
    
    AdminPC -->|Secure Access| VPN
    VPN --> WebAuth
    WebAuth --> TLS
    
    FieldDevices --> VLAN
    VLAN --> MQTTAuth
    VLAN --> OPCUASec
    VLAN --> ModbusSec
    
    TLS --> ServiceIso
    ServiceIso --> LogMon
    LogMon --> BackupSys
    
    style Firewall fill:#f96,stroke:#333,stroke-width:2px
    style TLS fill:#9f9,stroke:#333,stroke-width:2px
    style VPN fill:#9f9,stroke:#333,stroke-width:2px
```

---

⚡ **Performance Note**: The gateway is optimized for real-time industrial applications with microsecond timing requirements and 24/7 operation.

