# Smart Basketball Return Turret  
*A mechatronic system for sensing player position and rotating a ball-return turret using embedded control.*

---

## Demo — Final Working Prototype

*(Insert video or GIF here)*

The final version of this system detects whether the shooter is positioned to the **left** or **right** of the hoop and automatically rotates the turret toward them.  
It uses an **STM32 microcontroller**, **stepper motor control**, and a **GT2 belt-driven turret** mounted on a plywood frame with a turntable bearing.

This is the working prototype after three major design iterations.

---

## System Overview (Final Architecture)

### **Block Diagram**

<img width="1012" height="499" alt="image" src="https://github.com/user-attachments/assets/b248728c-a091-422d-9568-0f6ef44ae4da" />


## **Core Components**
- **Microcontroller:** STM32F103 ("Black Pill")  
- **Motor Driver:** A4988 with calibrated current limit  
- **Motor:** NEMA 17, 1.8° step, no microstepping  
- **Mechanics:** GT2 belt wrapped around turret, 20T motor output pulley  
- **Power:** 24V supply + MP1584 buck for MCU and IR system  
- **Sensors:** Modulated IR-based zone detection with TSOP receivers and IR LED

---

## How It Works

### **Firmware (Embedded C on STM32)**
- Timer-driven step pulses for consistent rotation
- Zone-based logic maps LEFT / RIGHT → angular step positions  
- Simple state machine: `IDLE → ROTATE → IDLE`  
- SWD debugging during tuning  


### **Electronics**
| ![Turret Schematic](https://github.com/user-attachments/assets/f5285817-24a9-4f3d-bb43-e2254af4fe85) |
|:---------------------------------------------------------------:|
| **Figure 1. Turret Schematic** |

| ![IR Modulation Schematic](https://github.com/user-attachments/assets/0af7e57e-3281-4f59-aa00-c0d7bbe3360d) |
|:---------------------------------------------------------------:|
| **Figure 2. IR Modulation Schematic** |

- Current limit tuned on A4988 (stable torque, reduced motor heating)  
- Proper grounding → solved intermittent MCU resets  
- MP1584 buck converter powers the MCU and IR system cleanly  
- Each IR zone sensor setup with pull-ups and interrupt/event handling  

### **Mechanical System**
<img width="456" height="423" alt="image" src="https://github.com/user-attachments/assets/3a3bd387-1fe1-40be-a7ec-4f59f1abe512" /> 
<img width="515" height="423" alt="image" src="https://github.com/user-attachments/assets/22e72ade-02b9-46eb-9889-bc6bd675ca22" />

- Turret inner-diameter ≈ 9.75 inches  
- Belt wraps around outer circumference  
- Stepper mounted with custom bracket  
- GT2 system ensures encoder-free accuracy

---

## Design Iterations

### **Version 1**

<img width="550" height="500" alt="image" src="https://github.com/user-attachments/assets/932e4dbd-b8c2-45f4-83d4-0e925a3877b4" />


**Problems:**
- Flimsy design, 1/16 aluminum too thin
- Much larger than necessary

**Outcome:**  
Redesigned before completion once it was clear this design would not work.

---


### **Version 2 — Improved Structure**
<img width="550" height="500" alt="image" src="https://github.com/user-attachments/assets/2807e3b7-4de7-40fc-88d3-df5bf1c80ff6" />

**Changes:**  
- Assembled from plywood
- Designed to hang further from the rim, allowing for much smaller radius
- Turret rotates internally with respect to mounted frame, further reducing size

**What improved:**
- Greatly reduced size and weight
- Sturdier and more impact-resistant 

**Problems:**
- Belt tension introduced fatal misalignment
- Too many potential points of failure
- Poor design and use of fasteners

**Outcome:**  
System almost worked, but when the GT2 belt was added, the tension pushed the whole turret assembly towards the motor, preventing proper movement.
Another redesign was needed.

---



### **Version 3 —  Working Prototype**
<img width="891" height="1029" alt="image" src="https://github.com/user-attachments/assets/1d5b98fb-5f51-46c3-8891-08cfb626d269" />

**Major Fixes:**
- Full remount of stepper → reduced wobble
- Turntable bearing fixed alignment issue
- Way fewer components, precision components cut with CNC router
- Smaller size and lower weight, simpler and more efficient design

**Result:**  
A reliable turret that smoothly rotates left/right on IR interrupt. Demonstrates fully functional embedded + mechanical integration.

---

## Key Challenges & Solutions

| Challenge | Solution |
|----------|----------|
| Belt tension introduced misalignment | Redesigned to use turntable bearing for alignment |
| IR noise and random triggers | Debugged and identified broken TSOP receivers |
| Belt slipping | Increased wrap angle |
| Motor wobble ruining accuracy | Rebuilt mount perpendicular to turret plane |
| Occasional MCU resets | Improved grounding and decoupling |

---

## Results

- **Consistent detection**  
- **Smooth, precise turret rotation**  
- **Stable stepper control**  
- **Reliable, repeatable positioning**  
- **Fully working demo**

---

## Takeaways

- Mechanical rigidity matters as much as firmware  
- IR sensing is limited; zone detection is more robust than analog readings  
- Debugging is faster with systematic versioning  
- Good grounding and power design prevent unpredictable MCU behavior

---

## Future Improvements

- Design a mount for a kicker + flywheel system, enable in code
- Replace IR with ToF or UWB for continuous position tracking indoors and outdoors
- Add PCB for cleaner wiring  
- Use microstepping for finer angle resolution  
- Add safety interlocks + calibration mode with limit switches
- Enclose system in rigid housing  

---
