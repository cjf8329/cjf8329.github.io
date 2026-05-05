# UrbanNAV — Offline Indoor Navigation via BLE Fingerprinting

*Robust indoor positioning using BLE signal fingerprints and on-device machine learning.*

![nRF52840](https://img.shields.io/badge/nRF52840-blue)
![Zephyr RTOS](https://img.shields.io/badge/Zephyr-RTOS-blue)
![BLE](https://img.shields.io/badge/BLE-GATT-blue)
![Embedded C](https://img.shields.io/badge/Embedded-C-blue)
![Android](https://img.shields.io/badge/Android-Kotlin-green)
![Fingerprinting](https://img.shields.io/badge/Algorithm-Fingerprinting-purple)

---

## Overview

Indoor positioning is fundamentally noisy.

Early in this project, we implemented:
- Trilateration  
- Weighted centroid  

Both approaches failed in real environments due to:
- Multipath interference  
- RSSI instability  
- Nonlinear signal decay  

**UrbanNAV ultimately pivoted to BLE fingerprinting**, which proved significantly more stable and accurate in practice.

The final system builds a radio map of the environment and matches live signal readings to known locations using a weighted k-NN approach.

---

## Why Fingerprinting Won

Traditional geometry-based methods assume clean distance estimates. In reality:

- RSSI does **not map cleanly to distance**
- Signals fluctuate based on obstacles, orientation, and reflections
- Small RSSI errors → large position errors

Fingerprinting avoids this entirely:

> Instead of estimating distance, we match patterns.

This made it the most reliable approach in testing, especially indoors.

---

## System Architecture

```
┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│  Beacon A   │   │  Beacon B   │   │  Beacon C   │
│ nRF52840    │   │ nRF52840    │   │ nRF52840    │
│ Zephyr RTOS │   │ Zephyr RTOS │   │ Zephyr RTOS │
└──────┬──────┘   └──────┬──────┘   └──────┬──────┘
       │                 │                 │
       └──── BLE Advertisements (RSSI) ───┘
                         │
                         ▼
              ┌────────────────────────┐
              │     Android App        │
              │  RSSI Vector Builder   │
              │  w-kNN Matching        │
              │  Position Estimate     │
              └──────────┬─────────────┘
                         ▼
                   (x, y position)
```

---

## Signal Processing Pipeline

```
Raw RSSI → 1D Kalman Filter → RSSI Vector
         → Fingerprinting (w-kNN)
         → 2D Adaptive Kalman Filter
         → UI Rendering
```

---

## Core Components

| Component            | Details                         |
|---------------------|--------------------------------|
| Microcontroller     | nRF52840 (Adafruit Feather)    |
| RTOS                | Zephyr RTOS                    |
| Radio               | BLE 5.0                        |
| Firmware Language   | Embedded C                     |
| Mobile App          | Android / Kotlin               |
| Algorithm           | Weighted k-NN Fingerprinting   |
| Filtering           | Kalman (1D + 2D)               |

---

## How It Works

### 1. Offline Survey (Fingerprint Collection)

- The environment is divided into grid points  
- At each point, RSSI values from all beacons are recorded  
- Each snapshot becomes a fingerprint vector

```
Location (x, y) → [RSSI₁, RSSI₂, RSSI₃, ...]
```

These are stored in a **radio map database**.

---

### 2. Online Positioning

- The phone scans nearby beacons  
- Builds a live RSSI vector  
- Compares it against stored fingerprints  

```kotlin
fun estimatePosition(current: Vector, database: List<Fingerprint>): Point {
    val neighbors = database.sortedBy {
        distance(it.vector, current)
    }.take(k)

    return weightedAverage(neighbors)
}
```

---

### 3. Filtering

- **1D Kalman Filter** smooths RSSI per beacon  
- **2D Kalman Filter** stabilizes position output  

Without this, the UI jittered heavily.

---

## Key Challenges

| Challenge | Insight |
|----------|--------|
| RSSI volatility | Signals fluctuate even when stationary |
| Device variance | Different phones measure RSSI differently |
| Multipath | Reflections dominate indoors |
| Calibration cost | Fingerprinting requires manual surveying |

---

## Results

- Fingerprinting significantly outperformed trilateration and centroid  
- Stable real-time positioning achieved indoors  
- Fully offline system (no server required)  
- Reliable under noisy RF conditions  

---

## Value Proposition

UrbanNAV demonstrates that:

> In real indoor environments, **data-driven localization beats geometry-based methods**.

The system provides:
- Infrastructure-light deployment  
- High stability in RF-heavy environments  
- Real-time positioning without internet  

---

## Future Improvements

- Sensor fusion (IMU + BLE)  
- Crowdsourced fingerprint updates  
- Automatic calibration  
- Floor detection via barometer  
- Transition to hybrid (fingerprinting + model-based)  

---

## Author

Christopher Fonseca  
NYU Tandon  
https://cjf8329.github.io  
