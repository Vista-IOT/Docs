---
layout: page
title: API Reference
permalink: /api-reference/
---

# Vista IoT Gateway API Reference

Complete API documentation for the Vista IoT Gateway platform, including REST endpoints and WebSocket events.

<div align="center">
  <img src="assets/images/logo.jpeg" alt="Vista IoT Logo" width="150" style="margin: 20px 0;"/>
</div>

## Base URL

```
http://YOUR_RADXA_IP:8080
```

## Authentication

Currently, the API does not require authentication by default. For production deployments, authentication can be enabled in the gateway configuration.

## REST API Endpoints

### Configuration Management

#### GET /api/config
Retrieve the current gateway configuration

**Example Request:**
```bash
curl http://192.168.1.100:8080/api/config
```

**Response:**
```json
{
  "gateway": {
    "device_id": "radxa_gateway_001",
    "device_name": "Radxa Industrial IoT Gateway",
    "location": "Factory Floor 1",
    "description": "Industrial IoT Gateway for protocol conversion",
    "system": {
      "hostname": "radxa-iot-gateway",
      "timezone": "UTC",
      "log_level": "INFO"
    },
    "networking": {
      "ethernet": {
        "enabled": true,
        "interface": "eth0",
        "dhcp": true
      },
      "wifi": {
        "enabled": false,
        "ssid": "",
        "security": "WPA2"
      },
      "firewall": {
        "enabled": true,
        "allowed_protocols": ["mqtt", "modbus", "opcua"]
      }
    },
    "protocols": {
      "mqtt": {
        "enabled": true,
        "broker": {
          "internal": true,
          "port": 1883
        },
        "topics": {
          "telemetry": "radxa/{device_id}/telemetry",
          "commands": "radxa/{device_id}/commands"
        }
      },
      "modbus": {
        "enabled": false,
        "servers": []
      },
      "opcua": {
        "enabled": false,
        "servers": []
      }
    }
  }
}
```

#### POST /api/config
Update the gateway configuration

**Example Request:**
```bash
curl -X POST http://192.168.1.100:8080/api/config \
  -H "Content-Type: application/json" \
  -d '{
    "gateway": {
      "device_id": "radxa_gateway_002",
      "device_name": "Updated Gateway Name",
      "location": "Building B",
      "protocols": {
        "mqtt": {
          "enabled": true,
          "broker": {
            "internal": true,
            "port": 1883
          }
        }
      }
    }
  }'
```

**Response:**
```json
{
  "status": "success",
  "message": "Configuration updated successfully"
}
```

**Error Response:**
```json
{
  "status": "error",
  "message": "Invalid configuration format"
}
```

### System Information

#### GET /api/system/status
Retrieve system status and metrics

**Example Request:**
```bash
curl http://192.168.1.100:8080/api/system/status
```

**Response:**
```json
{
  "system": {
    "hostname": "radxa-iot-gateway",
    "uptime": "5 days, 14:32:15",
    "cpu_usage": 15.2,
    "memory_usage": 45.8,
    "disk_usage": 28.5,
    "temperature": 52.3,
    "network_interfaces": {
      "eth0": {
        "status": "up",
        "ip_address": "192.168.1.100",
        "mac_address": "02:42:ac:11:00:02"
      }
    }
  },
  "services": {
    "radxa-gateway": "active",
    "radxa-dashboard": "active",
    "mosquitto": "active"
  },
  "protocols": {
    "mqtt": {
      "status": "connected",
      "broker": "localhost:1883",
      "clients_connected": 3
    },
    "modbus": {
      "status": "disabled"
    },
    "opcua": {
      "status": "disabled"
    }
  }
}
```

#### POST /api/system/reboot
Reboot the gateway system

**Example Request:**
```bash
curl -X POST http://192.168.1.100:8080/api/system/reboot
```

**Response:**
```json
{
  "status": "success",
  "message": "System reboot initiated"
}
```

### Protocol Testing

#### GET /api/protocols/test/{protocol_type}
Test connectivity for a specific protocol

**Supported protocol types:** `mqtt`, `modbus`, `opcua`, `dnp3`

**Example Request:**
```bash
curl http://192.168.1.100:8080/api/protocols/test/mqtt
```

**Response:**
```json
{
  "protocol": "mqtt",
  "status": "success",
  "details": {
    "broker": "localhost:1883",
    "connection_time": "12ms",
    "test_message_sent": true,
    "test_message_received": true
  }
}
```

**Error Response:**
```json
{
  "protocol": "modbus",
  "status": "error",
  "details": {
    "error_message": "Connection timeout to 192.168.1.50:502",
    "error_code": "CONNECTION_TIMEOUT"
  }
}
```

### Logs and Diagnostics

#### GET /api/logs
Retrieve recent system logs

**Query Parameters:**
- `lines` (optional): Number of log lines to retrieve (default: 100)
- `level` (optional): Log level filter (DEBUG, INFO, WARNING, ERROR)
- `service` (optional): Service name filter

**Example Request:**
```bash
curl "http://192.168.1.100:8080/api/logs?lines=50&level=ERROR"
```

**Response:**
```json
{
  "logs": [
    {
      "timestamp": "2024-01-15T10:30:15Z",
      "level": "ERROR",
      "service": "radxa-gateway",
      "message": "Failed to connect to Modbus device at 192.168.1.50:502"
    },
    {
      "timestamp": "2024-01-15T10:29:42Z",
      "level": "INFO",
      "service": "radxa-dashboard",
      "message": "Configuration updated successfully"
    }
  ],
  "total_lines": 2
}
```

## WebSocket Events

The gateway provides real-time updates via WebSocket connection at `/socket.io/`.

### Client Events (Send to Server)

#### connect
Establish WebSocket connection

```javascript
const socket = io('http://192.168.1.100:8080');

socket.on('connect', () => {
  console.log('Connected to gateway');
});
```

#### request_system_status
Request real-time system status

```javascript
socket.emit('request_system_status');
```

#### disconnect
Close WebSocket connection

```javascript
socket.disconnect();
```

### Server Events (Receive from Server)

#### config_updated
Triggered when configuration is updated

```javascript
socket.on('config_updated', (config) => {
  console.log('Configuration updated:', config);
});
```

#### system_status
Real-time system metrics

```javascript
socket.on('system_status', (status) => {
  console.log('System status:', status);
  // Update UI with real-time data
});
```

#### protocol_event
Protocol-specific events (device connections, data updates)

```javascript
socket.on('protocol_event', (event) => {
  console.log('Protocol event:', event);
  // Handle protocol-specific events
});
```

#### error
Error notifications

```javascript
socket.on('error', (error) => {
  console.error('Gateway error:', error);
});
```

## Protocol-Specific APIs

### MQTT Broker API

**Internal MQTT Broker:** `mqtt://YOUR_RADXA_IP:1883`

#### Default Topics

| Topic Pattern | Purpose | Example |
|---------------|---------|----------|
| `radxa/{device_id}/telemetry` | Device data | `radxa/gateway_001/telemetry` |
| `radxa/{device_id}/attributes` | Device attributes | `radxa/gateway_001/attributes` |
| `radxa/{device_id}/commands` | Device commands | `radxa/gateway_001/commands` |
| `radxa/{device_id}/status` | Device status | `radxa/gateway_001/status` |

#### Publish Data
```bash
# Publish telemetry data
mosquitto_pub -h 192.168.1.100 -t "radxa/gateway_001/telemetry" \
  -m '{"temperature": 23.5, "humidity": 60.2, "timestamp": "2024-01-15T10:30:00Z"}'

# Publish device status
mosquitto_pub -h 192.168.1.100 -t "radxa/gateway_001/status" \
  -m '{"status": "online", "last_seen": "2024-01-15T10:30:00Z"}'
```

#### Subscribe to Data
```bash
# Subscribe to all telemetry
mosquitto_sub -h 192.168.1.100 -t "radxa/+/telemetry"

# Subscribe to commands for specific device
mosquitto_sub -h 192.168.1.100 -t "radxa/gateway_001/commands"
```

### Modbus Protocol API

When Modbus is enabled, the gateway exposes Modbus TCP server functionality:

**Default Port:** `502`

#### Register Mapping

| Address | Function Code | Data Type | Description |
|---------|---------------|-----------|-------------|
| 0-99 | 4 (Input Registers) | int16 | System metrics |
| 100-199 | 3 (Holding Registers) | int16 | Configuration values |
| 200-299 | 1 (Coils) | bool | Protocol status flags |

#### Example Modbus Client
```python
from pymodbus.client.sync import ModbusTcpClient

client = ModbusTcpClient('192.168.1.100', port=502)
client.connect()

# Read system metrics
result = client.read_input_registers(0, 10, unit=1)
print(f"CPU Usage: {result.registers[0] / 10}%")
print(f"Memory Usage: {result.registers[1] / 10}%")

client.close()
```

### OPC-UA Server API

When OPC-UA is enabled, the gateway provides an OPC-UA server:

**Default Endpoint:** `opc.tcp://YOUR_RADXA_IP:4840`

#### Node Structure
```
Root
├── System
│   ├── CPU_Usage
│   ├── Memory_Usage
│   └── Temperature
├── Network
│   ├── Ethernet_Status
│   └── WiFi_Status
└── Protocols
    ├── MQTT_Status
    ├── Modbus_Status
    └── DNP3_Status
```

#### Example OPC-UA Client
```python
from opcua import Client

client = Client("opc.tcp://192.168.1.100:4840")
client.connect()

# Read CPU usage
cpu_node = client.get_node("ns=2;i=1001")
cpu_usage = cpu_node.get_value()
print(f"CPU Usage: {cpu_usage}%")

client.disconnect()
```

## Error Codes

| Code | Description | HTTP Status |
|------|-------------|-------------|
| `CONFIG_INVALID` | Invalid configuration format | 400 |
| `CONFIG_SAVE_FAILED` | Failed to save configuration | 500 |
| `PROTOCOL_DISABLED` | Requested protocol is disabled | 400 |
| `CONNECTION_FAILED` | Protocol connection failed | 500 |
| `SYSTEM_ERROR` | General system error | 500 |
| `PERMISSION_DENIED` | Insufficient permissions | 403 |

## Rate Limits

- **API Requests:** 100 requests per minute per IP
- **WebSocket Connections:** 10 concurrent connections per IP
- **MQTT Messages:** 1000 messages per minute per client

## SDK Examples

### Python SDK Example

```python
import requests
import json

class RadxaGateway:
    def __init__(self, host, port=8080):
        self.base_url = f"http://{host}:{port}"
    
    def get_config(self):
        response = requests.get(f"{self.base_url}/api/config")
        return response.json()
    
    def update_config(self, config):
        response = requests.post(
            f"{self.base_url}/api/config",
            json=config,
            headers={'Content-Type': 'application/json'}
        )
        return response.json()
    
    def get_system_status(self):
        response = requests.get(f"{self.base_url}/api/system/status")
        return response.json()

# Usage
gateway = RadxaGateway('192.168.1.100')
status = gateway.get_system_status()
print(f"CPU Usage: {status['system']['cpu_usage']}%")
```

### JavaScript SDK Example

```javascript
class RadxaGateway {
  constructor(host, port = 8080) {
    this.baseUrl = `http://${host}:${port}`;
  }
  
  async getConfig() {
    const response = await fetch(`${this.baseUrl}/api/config`);
    return response.json();
  }
  
  async updateConfig(config) {
    const response = await fetch(`${this.baseUrl}/api/config`, {
      method: 'POST',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify(config)
    });
    return response.json();
  }
  
  async getSystemStatus() {
    const response = await fetch(`${this.baseUrl}/api/system/status`);
    return response.json();
  }
}

// Usage
const gateway = new RadxaGateway('192.168.1.100');
gateway.getSystemStatus().then(status => {
  console.log(`CPU Usage: ${status.system.cpu_usage}%`);
});
```

