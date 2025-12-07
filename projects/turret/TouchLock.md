# TouchLock – Biometric Smart Lock  
*A Raspberry Pi–based access control system that uses fingerprint recognition, servo actuation, and email alerts for secure entry.*

---

## Demo — Final Working Prototype

*(Insert video or GIF here — e.g., demo.mp4)*

The prototype demonstrates a complete access-control flow:

- A user places their finger on the **optical fingerprint sensor**  
- If matched:
  - **Servo latch** unlocks  
  - **Green LED** turns on  
  - Text-to-speech: *“Access granted, <name>”*  
  - Email log is prepared

- If unmatched:
  - **Pi Camera** captures a photo of the intruder  
  - Email alert is sent with the image attached  
  - **Red LED** turns on  
  - Text-to-speech: *“Access denied”*  

Built and tested on standard Raspberry Pi models (any Pi with GPIO and UART support works; e.g., Pi 4, Pi 3B+, Pi Zero W).

---

## System Overview (Final Architecture)

### **Block Diagram**

*(Insert block diagram image here)*

---

## Core Components
- **Compute:** Raspberry Pi (any model with GPIO + UART)  
- **Biometric Sensor:** Optical fingerprint sensor using `adafruit_fingerprint`  
- **Actuator:** Servo driving a **simple demonstration latch**  
- **Camera:** Pi Camera module for intruder snapshots  
- **Indicators:** Red/Green LEDs for immediate feedback  
- **Notifications:** Gmail SMTP email alerts with optional photo attachment  
- **Software:**  
  - Python 3  
  - `adafruit_fingerprint`, `serial`, `RPi.GPIO`, `picamera`, `pyttsx3`, `smtplib`, `email.mime`, `pickle`

Project completed for an **intro engineering class** (two-person team; **I designed and implemented the majority of the system**, including firmware, hardware integration, and full software stack).

---

## How It Works

### **Main Control Loop (`main.py`)**
The `TouchLock` class coordinates all subsystems:

1. Waits for a fingerprint  
2. Reads the fingerprint template  
3. Searches for a matching ID  
4. Routes to **`grantAccess()`** or **`denyAccess()`**

#### **Grant Access**
- Turns **green LED** on  
- Announces authorization via TTS  
- Records the event  
- Drives the servo to unlock the latch  
- Returns to idle

#### **Deny Access**
- Captures `unauthorized.png` using Pi Camera  
- Sends email alert with image attached  
- Turns **red LED** on  
- Announces denial via TTS  
- Returns to idle

This loop runs indefinitely for real-time access control.

---

### **Fingerprint Management (`fingerprint_module.py`)**
Wraps the Adafruit fingerprint library into a higher-level sensor interface.

**Features:**
- Handles template capture & matching  
- Stores up to **127 enrolled users**  
- Persists names using a `pickle` database  
- Auto-corrects or initializes name storage on startup  
- Provides both verbose (interactive) and silent (production) capture modes  
- Includes a full setup driver for:
  - Enrollment  
  - Template lookup  
  - Deletion  
  - Testing  

---

### **Email Notifications (`email_module.py`)**
Modular email sender using Gmail SMTP (SSL):

- Builds multipart messages  
- Supports attachments (e.g., unauthorized user photo)  
- Used to log both access approvals and denials  

---

### **Servo + LED Control**
The hardware feedback layer ensures users always know the system state.

#### **Servo Module (`servo_module.py`)**
- GPIO-based PWM  
- Converts angles → servo duty cycles  
- Moves to “open” position temporarily  
- Auto-resets to a defined **closed latch** position

#### **LED Module (`LED_module.py`)**
- Simple red/green LED logic  
- Called directly by `grantAccess()` and `denyAccess()`  

---

## Mechanical System

*(Insert photos of the latch and physical mounting here)*

- Demo mechanism uses a **simple hinged latch** actuated by a hobby servo  
- Built for proof-of-concept in the class environment  
- 3D-printed and/or laser-cut parts used for clean mounting and repeatable motion  

---

## Design & Iteration History

### **Version 1 — Fingerprint Matching Prototype**
- Verified UART communication  
- Achieved reliable template enrollment and matching  
- No actuation, camera, or email yet  
- Used verbose terminal debugging

**Limitations:**  
- No feedback system  
- No event logging  
- No physical lock mechanism  

---

### **Version 2 — Integrated Access Control**
- Added servo for latch motion  
- Added red/green LEDs  
- Added text-to-speech  
- Created `TouchLock` class to unify logic

**Outcome:**  
Functional local-only access control system.

---

### **Version 3 — Final Demo With Intruder Logging**
- Added Pi Camera  
- Added email alerts + photo attachment  
- Improved LED timing + TTS flow  
- Cleaned up module interfaces and error handling  
- Added demo video and security camera screenshot

**Outcome:**  
A fully integrated biometric access system meeting all project requirements and going beyond the scope of the class.

---

## Key Challenges & Solutions

| Challenge | Solution |
|----------|----------|
| Fingerprint template management | Added persistent name database using `pickle` with auto-repair |
| Unclear feedback for users | Added TTS + LEDs to make access/denial unambiguous |
| Camera + sensor timing | Staggered photo capture before email send |
| Code complexity in one file | Broke project into reusable modules (`fingerprint_module`, `servo_module`, etc.) |
| Need for audit trail | Automatic unauthorized photo capture + emailed alert |

---

## Results
- Fully working biometric access system  
- Real-time lock/unlock behavior with servo latch  
- Logged security events with image evidence  
- Modular, well-documented Python codebase  
- Clear pathway to expand into a full smart lock product

---

## Takeaways
- Biometric systems require **robust feedback** to feel seamless  
- Hardware abstraction makes debugging dramatically easier  
- Persistent identity storage (names + templates) is crucial in real deployments  
- Even simple latch mechanisms benefit from consistent servo calibration  
- Adding a camera + email transforms a “demo” into a **real security device**

---

## Future Improvements
- Move from Gmail to a more secure, token-based email API  
- Replace PiCamera with modern `libcamera` pipeline  
- Add web dashboard for logs, user management, and remote unlock  
- Replace latch with a stronger mechanical actuator  
- Add second factor (PIN pad, smartphone unlock)  
- Build a custom PCB to replace loose wiring  
