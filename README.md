# 🧠 Brain-Controlled Robotic Arm Using Muse EEG & ESP8266

## 🚀 Project Overview
This project enables hands-free robotic arm control using **brain signals**. It integrates:
- **Muse EEG Headband** (for detecting eye blinks)
- **ESP8266 (NodeMCU)** (for robotic arm control via Wi-Fi)
- **Python** (for EEG signal processing & command transmission)

## 🎯 Features
✅ **EEG-based Robotic Arm Control**  
✅ **Wi-Fi Communication (ESP8266 Web Server)**  
✅ **Blink Detection using Muse EEG**  
✅ **Real-time Signal Processing with Python**  

---

## 🛠️ Hardware Components
| Component | Description |
|------------|------------------------------------------------|
| **Muse EEG Headband** | Brainwave sensing device |
| **ESP8266 (NodeMCU)** | Wi-Fi module controlling robotic arm |
| **Servo Motors (x2)** | Moves robotic arm based on EEG input |
| **External Power (5V, 2A)** | Powers servos (if high-torque) |
| **Breadboard & Jumper Wires** | For easy prototyping |

---

## 📡 System Architecture
1. **Muse EEG Headband** captures brain activity (eye blinks).
2. **Mind Monitor App** streams EEG data via OSC (Open Sound Control).
3. **Python Script (`live.py`)** processes EEG signals & detects blinks.
4. **ESP8266 Web Server** receives movement commands.
5. **Robotic Arm (Servo Motors)** moves accordingly.

---

## 🎛️ Circuit Connections (ESP8266 + Servo Motors)
| **ESP8266 Pin** | **Servo Motor 1** (Base Rotation) | **Servo Motor 2** (Arm Up/Down) |
|---------------|----------------|----------------|
| **Vin (5V)** | **Red Wire (+5V)** | **Red Wire (+5V)** |
| **GND** | **Black Wire (GND)** | **Black Wire (GND)** |
| **D1 (GPIO5)** | **Yellow Wire (Signal)** | - |
| **D2 (GPIO4)** | - | **Yellow Wire (Signal)** |

⚠️ **Power Considerations:** Use **external 5V 2A power** for high-torque servos.

### 📷 Wiring Diagram
![IMG-20250207-WA0010](https://github.com/user-attachments/assets/8a9ddf20-2bb4-4000-9773-fd30d03b7a18)


---

## 📲 Setting Up Muse EEG with Mind Monitor
1. **Download Mind Monitor** ([Android](https://play.google.com/store/apps/details?id=com.electroencephalography.mindmonitor) / [iOS](https://apps.apple.com/us/app/mind-monitor/id1190998549)).
2. **Pair Muse EEG Headband** via Bluetooth.
3. **Configure OSC Streaming:**
   - OSC Stream Target IP: `192.168.4.2`
   - OSC Stream Port: `5000`
   - Enable **Raw EEG Data Streaming**
4. **Start EEG Streaming** from the Mind Monitor app.

---

## 📡 IP Address Configuration
| **Device** | **IP Address** | **Purpose** |
|------------|--------------|----------------------|
| **ESP8266 (Robotic Arm Server)** | `192.168.4.3` | Receives movement commands |
| **Laptop/PC (Running Python script)** | `192.168.4.2` | Hosts OSC server for EEG processing |
| **Muse EEG (via Mind Monitor)** | `192.168.4.2:5000` | Streams EEG data via OSC |

---

## 📡 ESP8266 Wi-Fi Access Point Code (`portable_router.ino`)
```cpp
#include <ESP8266WiFi.h>
const char *ssid = "EEG_Control";
const char *password = "12345678";
WiFiServer server(80);
void setup() {
    Serial.begin(115200);
    WiFi.softAP(ssid, password);
    Serial.println("WiFi AP Started");
    server.begin();
}
void loop() {
    WiFiClient client = server.available();
    if (client) {
        String request = client.readStringUntil('\r');
        Serial.println(request);
        client.flush();
    }
}
```

---

## 🤖 ESP8266 Robotic Arm Control Code (`Final_code_arm_with_muse_eeg_control.ino`)
```cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>
#include <Servo.h>
ESP8266WebServer server(80);
Servo servo1, servo2;
void setup() {
    WiFi.begin("EEG_Control", "12345678");
    while (WiFi.status() != WL_CONNECTED) delay(500);
    server.on("/left", []() { servo1.write(45); server.send(200, "text/plain", "Moved Left"); });
    server.on("/right", []() { servo1.write(135); server.send(200, "text/plain", "Moved Right"); });
    server.on("/up", []() { servo2.write(45); server.send(200, "text/plain", "Moved Up"); });
    server.on("/down", []() { servo2.write(135); server.send(200, "text/plain", "Moved Down"); });
    server.on("/reset", []() { servo1.write(90); servo2.write(90); server.send(200, "text/plain", "Reset Position"); });
    server.begin();
}
void loop() { server.handleClient(); }
```

---

## 🎉 Impact
This project successfully enables **brain-controlled robotic arm movement** using EEG signals. It demonstrates a real-time **brain-computer interface** leveraging **Python, OSC, and ESP8266 Wi-Fi communication**.

🚀 **Next Steps:** Add **gesture-based controls** for more intuitive movement!
