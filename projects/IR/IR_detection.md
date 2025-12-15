# Thermal Object Detection & Logging System
*A Raspberry Pi–based thermal imaging system that detects, counts, and classifies nearby objects by size and temperature, with results logged to Google Sheets.*

[**→ View the GitHub Repository**](https://github.com/cjf8329/thermal-camera-object-counter)
---

The system performs a complete thermal sensing and logging pipeline:
- Continuously reads frames from the MLX90640 thermal camera
- Identifies warm objects within field of view
- Estimates approximate size and temperature
- Logs measurements to a Google Sheet via API for remote monitoring

Built and tested on a Raspberry Pi with I²C-connected thermal camera.

---

## System Overview

### **Block Diagram**

<img width="1057" height="685" alt="image" src="https://github.com/user-attachments/assets/21a79246-68cb-487a-8576-66e8149f1535" />



---

## Core Components
- **Compute:** Raspberry Pi
- **Thermal Sensor:** MLX90640 32x24 IR array
- **Interface**: I²C
- **Cloud Logging**: Google Sheets API
- **Software:**  
  - Python 3  
  - 'adafruit_mlx90640', 'numpy', 'pandas', 'gspread', Google API client libraries

Project completed for an **intro engineering class** (two-person team; I designed and implemented the electronics and firmware).

---

## How It Works

### **Main Control Loop (`main.py`)**
1. Read thermal frame from MLX90640
2. Convert raw data into temperature matrix
3. Apply thresholding to detect warm regions
4. Estimate object size from contiguous pixels
5. Increment object count
6. Push timestamped data to Google Sheets

The loop runs continuously, enabling real-time thermal monitoring and logging.

---

## Results
- Functional thermal object detection system
- Reliable temperature readings from IR array
- Automated cloud-based data logging
- Simple, extensible Python architecture

---

## Takeaways
- Low-resolution thermal arrays are still useful with good processing
- Cloud logging makes headless embedded work much easier
- Simple heuristics can work well for constrained sensors

---

## Future Improvements
- Add visualization dashboard
- Support additional sensors
- Package system for long-term deployment

---

## Source Code
Full implementation is available on GitHub:  
**https://github.com/cjf8329/thermal-camera-object-counter**
