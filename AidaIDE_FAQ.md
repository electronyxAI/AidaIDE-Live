# AidaIDE Frequently Asked Questions

> Complete guide to installation, troubleshooting, and features

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Installation by Device Type](#installation-by-device-type)
3. [Troubleshooting](#troubleshooting)
4. [Feature Guide](#feature-guide)
5. [Account & Subscription](#account--subscription)

---

## Getting Started

### What is AidaIDE?

AidaIDE is an IoT development ecosystem that lets you develop on your PC and deploy lightweight agents to your IoT devices with one click. No more juggling SSH sessions or manually syncing files.

**The architecture is simple:**
- **AidaIDE Full** runs on your PC (desktop + web interface)
- **AidaIDE Lite** deploys to your IoT devices (Raspberry Pi, Jetson, BeagleBone, etc.)
- **AidaIDE Micro** deploys to microcontrollers (ESP32, ESP8266)

### What devices are supported?

| Device | Agent | Status |
|--------|-------|--------|
| Raspberry Pi (all models) | Lite | ✅ Stable |
| NVIDIA Jetson (Nano, Xavier, Orin) | Lite | ✅ Stable |
| BeagleBone | Lite | ✅ Stable |
| Orange Pi | Lite | ✅ Stable |
| ESP32 | Micro | ✅ Stable |
| ESP8266 | Micro | 🧪 Beta |
| Any Linux SBC | Lite | ✅ Stable |

### What do I need to get started?

1. A valid AidaIDE subscription from [www.aidaide.com](https://www.aidaide.com)
2. AidaIDE Full installed on your PC (Windows, Mac, or Linux)
3. At least one IoT device on your network or connected via USB

---

## Installation by Device Type

### Raspberry Pi

**Method 1: One-Click Deploy (Recommended)**

1. Open AidaIDE Full on your PC
2. Go to **Tools → Deploy Lite → Select device**
3. Enter your Pi's IP address, username (`pi`), and password
4. Click **Deploy** — AidaIDE handles the rest

**Method 2: Command Line**

```bash
# On your PC
python lite_deployer.py deploy --host 192.168.1.50 --user pi --password raspberry
```

**Method 3: Manual Installation**

```bash
# SSH into your Pi
ssh pi@192.168.1.50

# Download and install
curl -sSL https://aidaide.com/install.sh | bash

# Or manually
git clone https://github.com/electronyx/AidaIDE
cd AidaIDE/aida-lite
pip3 install flask flask-socketio psutil
python3 server.py
```

**Access the web IDE:** Open `http://<pi-ip>:5050` in your browser

**Default credentials:**
- Username: `pi`
- Password: `raspberry` (change this!)

---

### NVIDIA Jetson (Nano, Xavier, Orin)

**Method 1: One-Click Deploy**

1. Open AidaIDE Full
2. **Tools → Deploy Lite**
3. Enter: Host IP, username (`jetson`), password
4. Click **Deploy**

**Method 2: Manual Installation**

```bash
# SSH into your Jetson
ssh jetson@192.168.1.60

# Install
curl -sSL https://aidaide.com/install.sh | bash
```

**Jetson-specific notes:**
- GPIO uses `Jetson.GPIO` library (auto-detected)
- CUDA acceleration available for supported features
- JetPack 4.x and 5.x both supported

---

### ESP32 / ESP8266

ESP devices use **AidaIDE Micro** (lighter footprint for microcontrollers).

**Requirements:**
- ESP32/ESP8266 with MicroPython installed
- USB cable connected to your PC

**Method 1: Serial Deploy from AidaIDE**

1. Connect ESP32 via USB
2. Open AidaIDE Full
3. **Tools → Deploy Micro**
4. Select your serial port (e.g., `COM3` or `/dev/ttyUSB0`)
5. Click **Deploy**

**Method 2: Quick Start Wizard**

```bash
python quick_start.py --scan-serial
```

Select your device and follow the prompts.

**Troubleshooting ESP32:**
- Make sure MicroPython is flashed (not Arduino firmware)
- Try different baud rates: 115200 (default), 9600, 460800
- Press the BOOT button while connecting if detection fails
- Check the USB cable supports data (not power-only)

---

### BeagleBone

**One-Click Deploy:**

1. **Tools → Deploy Lite**
2. Enter: Host IP, username (`debian`), password (`temppwd`)
3. Click **Deploy**

**Manual:**

```bash
ssh debian@192.168.1.70
curl -sSL https://aidaide.com/install.sh | bash
```

**BeagleBone-specific notes:**
- GPIO uses `sysfs` interface
- Cloud9 IDE can run alongside AidaIDE Lite (different ports)

---

### Generic Linux SBC (Orange Pi, Rock Pi, etc.)

Works with any ARM or x86 Linux single-board computer.

```bash
ssh root@192.168.1.80
curl -sSL https://aidaide.com/install.sh | bash
```

**Requirements:**
- Python 3.7+
- pip3
- Network connectivity

---

## Troubleshooting

### Connection Issues

#### "Connection refused" or "Connection timed out"

1. **Verify the device is on and connected to your network**
   ```bash
   ping 192.168.1.50
   ```

2. **Check SSH is running on the device**
   ```bash
   sudo systemctl status ssh
   sudo systemctl start ssh  # if not running
   ```

3. **Verify you're using the correct IP address**
   - Check your router's connected devices list
   - On the device: `hostname -I`

4. **Firewall issues**
   ```bash
   sudo ufw allow 22      # SSH
   sudo ufw allow 5050    # AidaIDE Lite web interface
   ```

#### "Authentication failed"

- Double-check username and password
- Default credentials by device:
  - Raspberry Pi: `pi` / `raspberry`
  - Jetson: `jetson` / varies
  - BeagleBone: `debian` / `temppwd`
- If you changed the password, use your new one
- Try SSH manually to confirm credentials work:
  ```bash
  ssh pi@192.168.1.50
  ```

#### "Host key verification failed"

This happens when a device's identity has changed (e.g., you reinstalled the OS).

```bash
# Remove the old key
ssh-keygen -R 192.168.1.50

# Try connecting again
ssh pi@192.168.1.50
```

---

### Serial / USB Issues

#### "No serial ports found"

1. **Check the USB cable** — some cables are power-only
2. **Install drivers** (Windows may need CH340 or CP2102 drivers)
3. **Check device manager** (Windows) or `ls /dev/tty*` (Linux/Mac)
4. **Try a different USB port**

#### "Permission denied" on Linux/Mac

```bash
# Add your user to the dialout group
sudo usermod -a -G dialout $USER

# Log out and back in, then try again
```

#### ESP32 not responding

1. Hold the **BOOT** button while plugging in USB
2. Try pressing **RESET** after connection
3. Check baud rate matches your firmware (usually 115200)
4. Verify MicroPython is installed, not Arduino firmware

---

### Lite Agent Issues

#### "AidaIDE Lite not starting"

1. **Check Python version** (requires 3.7+)
   ```bash
   python3 --version
   ```

2. **Install missing dependencies**
   ```bash
   pip3 install flask flask-socketio psutil
   ```

3. **Check if port 5050 is in use**
   ```bash
   sudo lsof -i :5050
   # Kill the process if needed
   sudo kill -9 <PID>
   ```

4. **Start manually and check for errors**
   ```bash
   cd ~/AidaIDE/aida-lite
   python3 server.py
   ```

#### "Can't access Lite web interface"

1. Verify Lite is running: `curl http://localhost:5050/api/health`
2. Check firewall allows port 5050
3. Try accessing from the device itself first
4. Make sure you're using `http://` not `https://`

---

### Network / Discovery Issues

#### "Device not found" in auto-discovery

mDNS discovery requires devices to be on the same network subnet.

1. **Check both devices are on the same network**
2. **Verify mDNS is working**
   ```bash
   # On your PC
   avahi-browse -a  # Linux
   dns-sd -B _aidaide._tcp  # Mac
   ```
3. **Fall back to manual IP entry** if mDNS fails

#### Cloud tunnel not connecting

1. Check your internet connection on both ends
2. Verify your subscription is active
3. Check if your network blocks outbound WebSocket connections
4. Try restarting AidaIDE Full and the Lite agent

---

### File Sync Issues

#### "Sync failed" or files not updating

1. **Check SSH connection** is still active
2. **Verify disk space** on the device:
   ```bash
   df -h
   ```
3. **Check file permissions**:
   ```bash
   ls -la /path/to/file
   ```
4. **Try manual sync** to see detailed errors

---

## Feature Guide

### Fleet Manager

See all your connected devices at a glance.

- **Device status**: Online/offline indicators
- **System stats**: CPU, RAM, disk usage, temperature
- **Quick actions**: Open terminal, browse files, deploy code

**To access:** Main window → Fleet Manager tab (or `Ctrl+F`)

---

### One-Click Deployment

Deploy AidaIDE Lite to any device without manual SSH commands.

1. **Tools → Deploy Lite**
2. Select connection method:
   - **SSH (Manual IP)**: Enter IP, username, password
   - **Auto-Discovery**: Scan network via mDNS
   - **Serial/USB**: For ESP32 and microcontrollers
3. Click **Deploy**

AidaIDE will automatically install dependencies, configure the agent, and start the service.

---

### File Sync

Push code from your PC to devices instantly.

- **Push**: Send files from AidaIDE Full → Device
- **Pull**: Retrieve files from Device → AidaIDE Full
- **Selective sync**: Choose specific files/folders

**Keyboard shortcut:** `Ctrl+Shift+S` to sync current file

---

### Terminal

Full PTY terminal access to any connected device.

- **Multiple tabs**: Connect to several devices simultaneously
- **Copy/paste**: Standard shortcuts work
- **Scrollback**: 10,000 lines of history
- **ANSI colors**: Full color support

**To open:** Right-click device → Open Terminal (or `Ctrl+T`)

---

### Serial Console

Direct serial communication for Arduino, ESP32, and other microcontrollers.

- **Auto-detect ports**: Scans for connected devices
- **Configurable baud rate**: 9600, 115200, etc.
- **Send commands**: Type directly or use macros
- **Log output**: Save serial output to file

**To access:** Tools → Serial Console

---

### GPIO Visualizer

Visual pin management for Raspberry Pi and compatible SBCs.

- **40-pin header view**: See all pins at a glance
- **Real-time states**: High/low indicators update live
- **Click to toggle**: Set outputs with one click
- **PWM control**: Adjust duty cycle and frequency
- **Pin labels**: I2C, SPI, UART functions labeled

**Supported platforms:**
- Raspberry Pi (RPi.GPIO)
- NVIDIA Jetson (Jetson.GPIO)
- BeagleBone (sysfs)
- Generic Linux (gpiod/sysfs)

---

### MQTT Explorer

Built-in MQTT client for IoT messaging.

- **Connect to any broker**: HiveMQ, Mosquitto, EMQX, or your own
- **Subscribe**: Monitor topics in real-time
- **Publish**: Send messages with custom payloads
- **Topic tree**: Visual hierarchy of all topics

**Public test brokers:**
| Broker | Host | Port |
|--------|------|------|
| HiveMQ | broker.hivemq.com | 1883 |
| Mosquitto | test.mosquitto.org | 1883 |
| EMQX | broker.emqx.io | 1883 |

---

### Code Intelligence

Smart coding features for faster development.

- **Syntax highlighting**: Python, C/C++, JavaScript, and more
- **Autocomplete**: Context-aware suggestions
- **Linting**: Real-time error detection
- **Go to definition**: Navigate your codebase
- **Outline view**: See file structure at a glance

---

### Version Control (Git)

Built-in Git integration.

- **Create versions**: Snapshot your code
- **Commit**: Stage and commit changes
- **Push/Pull**: Sync with remote repositories
- **Diff view**: See what changed

**To access:** Right-click file → Create Version, or use the Git panel

---

## Account & Subscription

### How do I activate my license?

1. Purchase a subscription at [www.aidaide.com](https://www.aidaide.com)
2. Open AidaIDE Full
3. Go to **Help → Activate License**
4. Enter your license key from your purchase confirmation email

### Can I use AidaIDE on multiple PCs?

Your license allows installation on multiple personal PCs, but only one active instance at a time. Contact support for team/enterprise licensing.

### How do I update AidaIDE?

AidaIDE checks for updates automatically. When an update is available:
1. You'll see a notification in the app
2. Click **Update Now** or go to **Help → Check for Updates**
3. AidaIDE will download and install the update

### Where can I get help?

| Resource | Link |
|----------|------|
| Documentation | [www.aidaide.com/docs](https://www.aidaide.com/docs) |
| Discord Community | [www.aidaide.com/discord](https://www.aidaide.com/discord) |
| Email Support | electronyxinc@gmail.com |
| GitHub Issues | [github.com/electronyx/AidaIDE](https://github.com/electronyx/AidaIDE) |

---

## Quick Reference

### Default Ports

| Service | Port |
|---------|------|
| AidaIDE Lite Web | 5050 |
| SSH | 22 |
| MQTT (standard) | 1883 |
| MQTT (TLS) | 8883 |

### Keyboard Shortcuts

| Action | Shortcut |
|--------|----------|
| Save file | `Ctrl+S` |
| Sync file | `Ctrl+Shift+S` |
| Open terminal | `Ctrl+T` |
| Fleet manager | `Ctrl+F` |
| Find in file | `Ctrl+F` |
| Go to line | `Ctrl+G` |
| Command palette | `Ctrl+Shift+P` |

### Useful Commands

```bash
# Check if Lite is running
curl http://<device-ip>:5050/api/health

# View Lite logs
journalctl -u aidaide-lite -f

# Restart Lite service
sudo systemctl restart aidaide-lite

# Check device IP
hostname -I

# Check Python version
python3 --version

# Check disk space
df -h

# Check memory
free -h

# Check CPU temperature (Raspberry Pi)
vcgencmd measure_temp
```

---

*Last updated: January 2025*

*Copyright © 2025 Electronyx Incorporated. All rights reserved.*
