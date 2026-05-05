# Embedded Systems & Computer Engineering Portfolio

Welcome! I’m a master's student in computer engineering focused on **embedded systems**, **firmware development**, and **robotics**.  
I build hardware–software systems that combine embedded sensing, real-time computation, and practical deployment in physical environments.

---

## Featured Project

### **UrbanNAV — Offline Indoor Navigation via BLE Fingerprinting**  
*Robust indoor positioning using BLE signal fingerprints and on-device processing.*

- nRF52840-based embedded BLE beacon system  
- Zephyr RTOS firmware in Embedded C  
- Android (Kotlin) mobile application for positioning  
- RSSI-based fingerprinting with weighted k-NN matching  
- 1D + 2D Kalman filtering for signal and position stabilization  
- Fully offline indoor navigation system (no server dependency)  

Early implementations used trilateration and weighted centroid methods, but both failed in real environments due to RSSI instability, multipath interference, and nonlinear signal behavior. The system was redesigned around **BLE fingerprinting**, which proved significantly more robust in practice.

The final system constructs a radio map of the environment and matches live RSSI vectors against stored fingerprints to estimate position.

**[View Full Project →](projects/UrbanNAV/urbannav.md)**

---

## Skills & Technical Focus

**Embedded & Firmware:**  
- STM32 (HAL + bare-metal C), ARM Cortex-M  
- nRF52 (Zephyr RTOS, BLE systems)  
- Timers, interrupts, peripherals, driver development  
- UART / I2C / SPI communication  
- Real-time embedded systems and state machines  

**Wireless & Systems:**  
- BLE GATT architecture  
- RSSI processing and signal filtering  
- Sensor fusion and Kalman filtering  
- Embedded localization systems  

**Software:**  
- C / C++, Python  
- Android development (Kotlin)  
- Git, Linux, scripting  

**Electronics & Mechatronics:**  
- Sensor integration and embedded hardware debugging  
- Motor control systems (steppers, drivers)  
- Mechanical prototyping (3D printing, CAD with Fusion 360)  
- Actuated systems and embedded electromechanical design  

---

## Other Projects

### Smart Basketball Return Turret  
Infrared-based automated ball return system with embedded motor control and directional sensing.  
[View Project](projects/turret/turret.md)

---

### TouchLock  
Fingerprint-enabled IoT door lock system with embedded authentication and wireless control.  
[View Project](projects/TouchLock/TouchLock.md)

---

### Thermal Object Detection & Logging System  
Thermal imaging-based detection system with automated data logging to Google Sheets.  
[View Project](projects/IR/IR_detection.md)

---

## Resume  
**[Download Resume (PDF)](Christopher_Fonseca_Resume.pdf)**

---

## Contact  
- **Email:** cjf8329@nyu.edu  
- **GitHub:** https://github.com/cjf8329  
- **LinkedIn:** https://www.linkedin.com/in/christopherfonseca/
