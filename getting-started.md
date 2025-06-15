---
layout: page
title: Getting Started
permalink: /getting-started/
---

# Getting Started with Vista IoT

This guide will help you deploy the Vista IoT Gateway Platform on your Radxa Single Board Computer in minutes.

<div align="center">
  <img src="{{ '/assets/images/industry.png' | relative_url }}" alt="Vista IoT Industrial Setup" width="500" style="margin: 20px 0;"/>
</div>

## Prerequisites

### Hardware Requirements
- **Radxa SBC** with Armbian/Debian-based Linux
- **Minimum 2GB RAM** (4GB recommended)
- **8GB+ microSD card** (32GB+ recommended for production)
- **Internet connection** for initial setup
- **Root access** to the system

### Supported Hardware Platforms
- **Radxa Cubie A5E** (Primary - Industrial grade)
- **Banana Pi BPI-F3** (RISC-V alternative)
- **Radxa ROCK Series** (Development & testing)
- **Other ARM SBCs** (Community supported)

> 📝 **Detailed hardware information**: See our [Supported Hardware](hardware) page for comprehensive board specifications, performance benchmarks, and selection guidance.

### Network Requirements
- Ethernet connection (WiFi optional)
- Access to ports: 8080 (Web Dashboard), 1883 (MQTT), 502 (Modbus), 4840 (OPC-UA)

## 🚀 Quick Deployment

### Method 1: One-Command Installation

```bash
# Download and run the deployment script
wget -O - https://raw.githubusercontent.com/Vista-IOT/radxa-iot-gateway/main/deploy.sh | sudo bash
```

### Method 2: Manual Installation

#### 1. Clone the Repository

```bash
# Clone the repository
git clone https://github.com/Vista-IOT/radxa-iot-gateway.git
cd radxa-iot-gateway

# Make deploy script executable
chmod +x deploy.sh
```

#### 2. Deploy the Complete System

```bash
# Deploy the complete system (run as root)
sudo ./deploy.sh deploy
```

The deployment script will:
- Install system dependencies
- Set up Python virtual environment
- Install Python packages
- Build the web dashboard
- Configure systemd services
- Set up firewall rules
- Create configuration directories

#### 3. Access the Web Dashboard

After deployment (takes 5-10 minutes):

```bash
# Find your Radxa's IP address
ip addr show

# Or use hostname
hostname -I
```

Open your browser and navigate to:
- **URL**: `http://YOUR_RADXA_IP:8080`
- **Default Login**: No authentication required (configurable later)

## 🖥️ Initial Configuration

### 1. Device Information Setup

1. Open the web dashboard
2. Navigate to **Overview** tab
3. Configure:
   - **Device ID**: `radxa_gateway_001`
   - **Device Name**: `Factory Floor Gateway`
   - **Location**: `Building A - Floor 1`
   - **Description**: Brief description of the gateway's purpose

### 2. Network Configuration

1. Go to **Network** tab
2. Configure Ethernet:
   - **Enable DHCP** for automatic IP assignment
   - Or set **Static IP** with your network settings
3. Configure WiFi (if needed):
   - Enter **SSID** and **Password**
   - Select **Security Type** (WPA2 recommended)
4. Set **DNS Servers** (default: 8.8.8.8, 8.8.4.4)

### 3. Enable Industrial Protocols

1. Navigate to **Protocols** tab
2. Enable required protocols:
   - **MQTT**: For IoT messaging
   - **Modbus TCP/RTU**: For PLC communication
   - **OPC-UA**: For industrial automation
   - **DNP3**: For SCADA systems
   - **IEC 61850**: For power system communication

### 4. Apply Configuration

1. Click **Save Configuration** to store settings
2. Click **Apply & Restart** to implement changes
3. System will automatically configure and reboot

## 🔧 Verification Steps

### Check Service Status

```bash
# Check gateway services
sudo systemctl status radxa-gateway
sudo systemctl status radxa-dashboard

# Check MQTT broker
sudo systemctl status mosquitto

# View logs
sudo journalctl -u radxa-gateway -f
sudo journalctl -u radxa-dashboard -f
```

### Test Protocol Connectivity

```bash
# Test MQTT
mosquitto_pub -h localhost -t test -m "hello"
mosquitto_sub -h localhost -t test

# Check open ports
sudo ss -tlnp | grep -E ':(8080|1883|502|4840)'
```

### Web Dashboard Health Check

1. Access dashboard at `http://YOUR_IP:8080`
2. Check **System Status** shows "Online"
3. Verify **Protocol Status** shows enabled protocols
4. Review **Network Status** for connectivity

## 🔄 Next Steps

### Connect Your First Device

1. **For Modbus Devices**:
   - Go to **Protocols** → **Modbus**
   - Add device with IP address and unit ID
   - Configure register mappings

2. **For OPC-UA Servers**:
   - Go to **Protocols** → **OPC-UA**
   - Add server endpoint URL
   - Configure node subscriptions

3. **For MQTT Devices**:
   - Use broker at `YOUR_IP:1883`
   - Subscribe to topics: `radxa/{device_id}/telemetry`
   - Publish commands to: `radxa/{device_id}/commands`

### Advanced Configuration

- **Security Setup**: Configure SSL/TLS, user authentication
- **Data Storage**: Set up InfluxDB or PostgreSQL
- **Cloud Integration**: Connect to AWS IoT, Azure IoT Hub
- **Custom Protocols**: Extend with additional protocol support

### Learning Resources

- [Architecture Overview](architecture) - Understand the system design
- [Protocol Configuration](tutorials#protocol-setup) - Detailed protocol guides
- [API Reference](api-reference) - Integration and automation
- [Troubleshooting Guide](contributing#troubleshooting) - Common issues and solutions

## ⚙️ Development Setup

For development and customization:

```bash
# Set up development environment
cd radxa-iot-gateway
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Start development server
python app.py

# In another terminal, start web dashboard development
cd web-dashboard
npm install
npm run dev
```

---

⚠️ **Important**: This is an industrial-grade IoT platform. Always test configurations in a development environment before deploying to production systems.

