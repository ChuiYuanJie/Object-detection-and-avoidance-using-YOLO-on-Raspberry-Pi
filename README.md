# Edge AI: Distributed Object Detection & Hardware Control
**Mechatronics Engineering | Computer Vision & IoT**

This project implements a distributed vision system where a high-performance host (Laptop) performs real-time object detection and sends trigger signals to an edge device (Raspberry Pi) via **Socket Programming** for hardware actuation.

### Project Presentation & Technical Setup
This presentation covers the full end-to-end implementation, including Raspberry Pi OS imaging, YOLO environment configuration, and Socket communication architecture.
👉 [**View Full Project Slides on Canva**](https://canva.link/2xnrl1v7azphi8u)

### Project Features
* **Real-time YOLO Detection:** Uses YOLOv5/v11 for high-speed object identification.
* **Monocular Distance Estimation:** Implemented a triangle similarity geometry model to estimate the distance between the camera and the object based on perceived pixel width.
* **Socket Communication:** Low-latency TCP/IP connection to bridge the gap between Python/OpenCV and RPi.GPIO.

### System Architecture
The system is divided into two main nodes:

#### **1. The Vision Node (Host Laptop)**
* **Role:** Captures video, runs inference, and calculates distance.
* **Logic:** If a "cell phone" is detected within a **25cm threshold**, a signal is sent over the network to the Pi.
* **Key Math:** $$Focal Length = \frac{Distance_{real} \times Width_{pixels}}{Width_{real}}$$
  $$Distance = \frac{Width_{real} \times Focal Length}{Width_{pixels}}$$

#### **2. The Actuation Node (Raspberry Pi)**
* **Role:** Listens for network triggers and controls physical hardware.
* **Logic:** Receives the distance string, parses the data, and toggles **GPIO 17** (LED/Relay) based on the proximity logic.

### Technical Challenges & Troubleshooting
* **The "Multiple Detection" Crash:** During testing, the system encountered stability issues when detecting multiple objects simultaneously. 
* **Root Cause:** The `socket.sendall()` function triggered multiple transmissions per frame, causing buffer overflow or string-parsing errors on the Raspberry Pi side when data was concatenated (e.g., `LED_ON:20.2LED_ON:15.5`).
* **Engineering Fix:** Implemented a `detected = False` flag and a logic gate to prioritize the *closest* detected object, ensuring only one clean socket message is sent per frame.
