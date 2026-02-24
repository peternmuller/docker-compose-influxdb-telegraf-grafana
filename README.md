# IoT Stack with LoRa, Zigbee, MQTT and Monitoring

This repository contains a Docker Compose setup to create a full LoRa/Zigbee + MTIG + Node-RED IoT stack. 

## Quick Start

### Clone the Repository

```bash
git clone https://github.com/peternmuller/multiprotocol-iot-gateway-docker.git
cd multiprotocol-iot-gateway-docker
```

### Deploy the Stack

```bash
docker network create monitoring
```

```bash
docker compose up -d
```

![Deployment Output](https://github.com/user-attachments/assets/0a9f8a91-66f6-4aec-a71c-16f5d6051b59)

## Detailed Setup Guide

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/peternmuller/multiprotocol-iot-gateway-docker.git
   cd multiprotocol-iot-gateway-docker
   ```

2. **Start the Services**:
   ```bash
   docker network create monitoring
   docker compose up -d
   ```

3. **Verify the Services**:
   ```bash
   docker compose ps
   ```

4. **Access Grafana**:
   - Open your browser and navigate to `http://localhost:3000`
   - Default credentials: username and password are both `admin`

## Using the Repository

### Configuring Telegraf

- Telegraf configuration files are located in the `telegraf` directory.
- After modifying the configuration files, restart the Telegraf container:
  ```bash
  docker compose restart telegraf
  ```

### Configuring Zigbee2MQTT

**Important**: Zigbee2MQTT requires a Zigbee USB adapter to be plugged into your host machine for the Docker container to start successfully.

1. **Identify Your USB Adapter**:
   - Determine your adapter's location by following the [Zigbee2MQTT adapter identification guide](https://www.zigbee2mqtt.io/guide/configuration/adapter-settings.html#determine-location-of-the-adapter).
   - Update the `devices` mapping in [docker-compose.yml](docker-compose.yml) with your adapter's actual location (currently set to a Sonoff Zigbee 3.0 USB Dongle Plus V2).

2. **Configure the Service**:
   - A `configuration.yaml` file will be automatically created in the `zigbee2mqtt_data/` directory on first run.
   - Ensure the following settings are present in `zigbee2mqtt_data/configuration.yaml`:
     ```yaml
     mqtt:
       server: mqtt://mosquitto:1883  # Use the Docker service name, not localhost
     frontend:
       enabled: true
       port: 8888  # To match docker-compose.yml (default port 8080 conflicts with ChirpStack)
     onboarding: false  # Disable onboarding; configure directly in configuration.yaml
     ```

3. **Access the Frontend**:
   - Once running, open http://localhost:8888 to access the Zigbee2MQTT web interface.

### Viewing Metrics in Grafana

1. Open Grafana and add InfluxDB as a data source.
2. Create dashboards and panels to visualize the metrics collected by Telegraf.

## Telegraf Configuration for Different Environments

We provide detailed guides for configuring Telegraf in various environments:

- [Telegraf on Ubuntu](docs/install_telegraf_on_ubuntu.md)
- [Telegraf on Windows Subsystem for Linux (WSL) - Debian](docs/install_telegraf_on_wsl_linux.md)
- [Telegraf on Windows](docs/install_telegraf_on_windows.md)
- [Telegraf in Docker](docs/explaining_telegraf.md#monitorizar-contenedores-docker)

For a comprehensive explanation of Telegraf configuration across different platforms, refer to our [Telegraf Configuration Guide](docs/explaining_telegraf.md).

## Service Descriptions

### IoT Protocol Support
1. **ChirpStack**: LoRaWAN network server and application server for managing LoRa devices and gateways.
2. **ChirpStack Gateway Bridge**: Bridges LoRa gateways to ChirpStack via MQTT (supports standard MQTT and Basic Station protocols).
3. **ChirpStack REST API**: RESTful API interface for remote integration and control.
4. **Zigbee2MQTT**: Converts Zigbee protocol to MQTT for standardized IoT messaging.

### Messaging & Storage
5. **Mosquitto**: MQTT broker for pub/sub messaging between IoT services, gateways, and applications.
6. **PostgreSQL**: Primary database for ChirpStack application data, user accounts, and device configurations.
7. **Redis**: In-memory cache for ChirpStack session management and real-time data.

### Monitoring
8. **Telegraf**: Agent for collecting metrics from the Docker host, containers, and services, forwarding to InfluxDB.
9. **InfluxDB**: Time-series database optimized for high-volume metrics and telemetry storage.
10. **Grafana**: Web-based visualization and dashboard platform for creating real-time monitoring views.

### Automation
11. **Node-RED**: Low-code visual programming environment for workflow automation and integration between services.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
