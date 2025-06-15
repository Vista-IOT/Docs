---
layout: page
title: API Reference
permalink: /api-reference/
---

# API Reference

Complete API documentation for Vista-IOT platform.

## Base URL

```
https://api.vista-iot.com/v1
```

## Authentication

All API requests require authentication using API keys:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
     https://api.vista-iot.com/v1/devices
```

## Endpoints

### Devices

#### GET /devices
Retrieve all devices

**Response:**
```json
{
  "devices": [
    {
      "id": "device-123",
      "name": "Temperature Sensor 1",
      "type": "temperature",
      "status": "online",
      "last_seen": "2023-01-01T12:00:00Z"
    }
  ]
}
```

#### POST /devices
Register a new device

**Request:**
```json
{
  "name": "New Sensor",
  "type": "humidity",
  "location": "Room A"
}
```

### Data

#### GET /data/{device_id}
Retrieve sensor data for a specific device

**Parameters:**
- `device_id` (required): Device identifier
- `start_time` (optional): Start timestamp
- `end_time` (optional): End timestamp
- `limit` (optional): Maximum number of records

**Response:**
```json
{
  "data": [
    {
      "timestamp": "2023-01-01T12:00:00Z",
      "value": 23.5,
      "unit": "celsius"
    }
  ]
}
```

