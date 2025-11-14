# ESP32 Redundant Monitoring (MQTT)

Two ESP32 boards (robot1, robot2) communicate via MQTT. Each publishes a heartbeat and simulated sensor data. If a board detects the other stopped heartbeating, it publishes an alert to `robots/alerts`.

## Contents
- `arduino/robot1/robot1.ino` - code for Robot 1
- `arduino/robot2/robot2.ino` - code for Robot 2
- `mosquitto/mosquitto.conf` - sample broker config
- `node-red/node_red_flow.json` - Node-RED flow (import)
- `LICENSE` - MIT

## Requirements
- Arduino IDE with ESP32 board support
- Libraries: PubSubClient (install via Library Manager)
- Mosquitto MQTT broker (running on your PC)
- (Optional) Node-RED + Node-RED dashboard

## Quick setup

1. **Set broker IP in sketches**
   - Edit `robot1.ino` and `robot2.ino`: set `mqtt_server` to your PC IP (e.g., `10.80.254.160`).

2. **Start mosquitto**
   - Windows example:
     ```powershell
     # run with config file path
     "C:\Program Files\mosquitto\mosquitto.exe" -c "C:\Program Files\mosquitto\mosquitto.conf" -v
     ```

3. **Upload code**
   - Open `robot1.ino` in Arduino IDE, set board to ESP32 Dev Module, choose COM port, Upload.
   - Do the same for `robot2.ino` on the second board.

4. **Verify messages**
   - On PC, use mosquitto_sub:
     ```powershell
     mosquitto_sub -h localhost -t robots/+/data -v
     mosquitto_sub -h localhost -t robots/alerts -v
     ```

5. **Optional: Node-RED**
   - Import `node_red_flow.json`.
   - Configure MQTT broker (127.0.0.1:1883).
   - Deploy and open dashboard.

## How detection works
- Each robot publishes a retained heartbeat `robots/<id>/heartbeat` every 2s.
- Each robot subscribes to the peer heartbeat and stores the last timestamp.
- If peer heartbeat not seen for `heartbeatTimeout` (~7s), the robot publishes an alert to `robots/alerts`.

## To prepare for GitHub
```bash
# from project root
git init
git add .
git commit -m "Initial commit - two ESP32 redundant monitoring"
# create repo on GitHub, then push:
git remote add origin https://github.com/<you>/<repo>.git
git branch -M main
git push -u origin main
