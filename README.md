# 📡 Sistema de Red Mesh LoRa - TFG Telecomunicaciones

Sistema completo de comunicación inalámbrica basado en LoRa que integra dispositivos embebidos (ESP32), gateways (Raspberry Pi) y procesamiento de datos (MacBook) para crear una red mesh de monitoreo IoT.

---

## 🏗️ Arquitectura del Sistema

```
┌─────────────┐      LoRa       ┌──────────────────┐      MQTT      ┌─────────────┐
│   ESP32     │ ───────────────▶│  Raspberry Pi    │ ──────────────▶│   MacBook   │
│  (Nodos)    │  868 MHz        │  (Gateway)       │   JSON         │  (Proceso)  │
│  Mesh LoRa  │                 │  SX1262 Receiver │                │             │
└─────────────┘                 └──────────────────┘                └─────────────┘
                                                                           │
                                                                           ▼
                                                                   ┌──────────────┐
                                                                   │  InfluxDB    │
                                                                   │  + Grafana   │
                                                                   └──────────────┘
```

---

## 📦 Estructura del Proyecto

### 1. **Lora-Mesh/** - Nodos Emisores ESP32

Código para dispositivos **Heltec WiFi LoRa 32 V3 (ESP32-S3)** que:

- Transmiten datos de sensores (GPS, MPU6050) mediante LoRa
- Forman una red mesh con reenvío automático de mensajes
- Implementan mecanismos anti-colisión y anti-loop
- Operan en la banda 868 MHz (EU868)

**Tecnologías**: C++ (PlatformIO), RadioLib, LoRa SX1262

**Ver más**: [Lora-Mesh/README.md](Lora-Mesh/README.md)

---

### 2. **Receiver-Lora_RaspberryPi/** - Gateway Receptor

Código en **Raspberry Pi** que:

- Recibe paquetes LoRa desde los nodos ESP32
- Parsea payloads binarios (13 bytes) con datos GPS y estado
- Publica los datos en un broker MQTT para procesamiento posterior
- Mide RSSI (fuerza de señal) de cada paquete

**Tecnologías**: Python 3.8+, SX1262 HAT, paho-mqtt

**Ver más**: [Receiver-Lora_RaspberryPi/README.md](Receiver-Lora_RaspberryPi/README.md)

---

### 3. **MQTT-Raspberry/** - Procesamiento y Visualización

Sistema en **MacBook** (Docker) que:

- Suscribe a mensajes MQTT del broker en Raspberry Pi
- Almacena datos en **InfluxDB** (base de datos de series temporales)
- Visualiza datos en tiempo real mediante **Grafana**
- Procesa datos GPS, estados de sensores y métricas de red

**Tecnologías**: Docker, Python, InfluxDB 2.7, Grafana

**Ver más**: [MQTT-Raspberry/README.md](MQTT-Raspberry/README.md)

---

## 🔄 Flujo de Datos

1. **ESP32** → Transmite paquetes LoRa con datos GPS, sensores y estado
2. **Raspberry Pi** → Recibe paquetes LoRa, los parsea y publica en MQTT
3. **MacBook** → Suscribe a MQTT, procesa y almacena en InfluxDB
4. **Grafana** → Visualiza datos históricos y en tiempo real

### Formato de Datos

Los paquetes contienen:

- **ID del nodo** (`src`)
- **Número de secuencia** (`seq`)
- **Time To Live** (`ttl`)
- **Coordenadas GPS** (`lat`, `lon`)
- **Estado del nodo** (`state`: 0=OK, 1=SOS)
- **RSSI** (fuerza de señal)

---

## 🚀 Inicio Rápido

### 1. Configurar Nodos ESP32

```bash
cd Lora-Mesh
# Editar include/lora_config.h para configurar MY_ID
pio run --target upload
```

### 2. Configurar Gateway Raspberry Pi

```bash
cd Receiver-Lora_RaspberryPi
# Configurar .env con datos MQTT
python3 receiver_lora.py
```

### 3. Iniciar Sistema de Procesamiento

```bash
cd MQTT-Raspberry
# Configurar .env con IP de Raspberry Pi y tokens InfluxDB
docker compose up -d
```

---

## 📋 Requisitos del Sistema

### Hardware

- **ESP32**: Heltec WiFi LoRa 32 V3 (uno o más nodos)
- **Raspberry Pi**: Modelo 3B+ o superior con SX1262 HAT
- **MacBook**: Para ejecutar Docker con InfluxDB y Grafana

### Software

- **PlatformIO** (para ESP32)
- **Python 3.8+** (para Raspberry Pi)
- **Docker & Docker Compose** (para MacBook)
- **Broker MQTT** (Mosquitto en Raspberry Pi)

---

## 🔧 Configuración LoRa

Todos los componentes deben usar los mismos parámetros:

```
Frecuencia:      868.0 MHz (EU868)
Spreading Factor: 12 (SF12)
Bandwidth:       125 kHz
Coding Rate:     4/5
Sync Word:       0x12
CRC:             Habilitado
```

---

## 📚 Documentación

Cada proyecto incluye su propio README con:

- Instrucciones de instalación detalladas
- Configuración específica
- Troubleshooting
- Referencias técnicas

---

## 👤 Autor

**Oscar Jiménez Bou**  
Trabajo de Fin de Grado - Telecomunicaciones

---

## 📄 Licencia

Este proyecto es parte de un trabajo académico (TFG).

---

**Para más información, consulta los README individuales de cada proyecto.**
