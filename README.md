# Wireless Bio-Signal Recorder & Test Bench  
**Author:** Stephen Qiao  
---

## 1. Executive Summary
- **Goal:** Brief description of the system (wireless PCB-based biosignal recorder, BLE streaming, Python test suite).  
- **Key Deliverables:** PCB, ESP32 firmware, Python visualization/test bench.  
- **Applications:** EEG-like bio-signal capture for research, prototyping, and test validation.  

---

## 2. System Architecture
- **Block Diagram** (AFE → ADC/ESP32 → BLE → Host).  
- **Signal Path Overview:** Sensor → Amplifier → Filter → ADC → Wireless → Desktop.  
- **Requirements:**  
  - **Amplitude (input):** 10-100 µV_pp (up to ~300 µV_pp)
  - **Bandwidth:** 0.5-40 Hz (optional extended to 100 Hz)
  - **Electrode DC offset tolerance:** ±300 mV
  - **Common-mode tolerance:** ±1.5 V (DRL active)
  - **Input impedance:** ≥100 MΩ @ 10 Hz
  - **AFE gain:** Selectable ×200 / ×1000 (INA333: R_G ≈ 503 Ω / 100 Ω)
  - **Analog HPF:** 0.5 Hz (R=1 MΩ, C=0.33 µF)
  - **Analog LPF:** 40 Hz (R=10 kΩ, C=0.39 µF), optional 100 Hz (R=10 kΩ, C=0.15–0.16 µF)
  - **Mains suppression:** Digital 50/60 Hz notch (IIR)
  - **ADC:** External delta-sigma recommended (ADS1115 16-bit or ADS1299 24-bit)
  - **Sampling rate:** 500 Hz
  - **Noise target:** ≤1 µV_rms (0.5–40 Hz), CMRR ≥90 dB
  - **Safety:** Battery power, USB isolation, input series resistors + TVS

---

## 3. Hardware Design
### 3.1 Analog Front-End (AFE)
- Instrumentation amplifier choice:
#### Why INA333 (vs AD620) for EEG AFE
EEG signals are tiny (10-300 µVpp, 0.5-40 Hz), so the instrumentation amplifier must provide high CMRR, low noise, and stable baseline at low frequency.

##### Key Equations
- **INA333:**
  $G = 1 + \frac{100kΩ}{R_G}$
- **AD620:**
  $G = 1 + \frac{49.4kΩ}{R_G}$

##### Comparison
| Aspect | INA333 | AD620 |
|--------|--------|-------|
| Supply | 2.7–5.5 V, single-supply | ±2.3–18 V (best ±5 V) |
| Rail-to-rail | Yes | No |
| Offset & drift | Very low (zero-drift) | Higher |
| 0.1–10 Hz stability | Excellent | Moderate drift |
| Wideband noise | Higher | Lower |
| Power use | Very low (battery-friendly) | Higher |
| Use case | Portable, 3.3 V EEG | Lab setup, ±5 V |

##### Decision
- **INA333:** chosen for portable, low-power, 3.3 V BLE system
- **AD620:** useful alternative if using ±5 V bench supplies and prioritizing wideband noise.

### Filtering stages (high-pass, low-pass)
To preserve EEG signals (10–300 µV, 0.5–40 Hz) while protecting the amplifier and ADC, two filters are included:

#### High-Pass Filter (HPF)
- Purpose: remove electode DC offsets and slow baseline drift
- Cutoff: ~0.5 Hz

#### Low-Pass Filter (LPF, Anti-Alias)
- Purpose: reject high-frequency noise and prevent aliasing before ADC
- Cutoff: ~40-100 Hz

#### Signal Flow 
Filter: HPF → Buffer → LPF. 

### Gain Design 
- **Expected input (EEG):** 10–300 µVpp
- **Target output swing:** 0.3 Vpp
- **AFE gain:** ×1000 (INA333)  
- **Gain resistor (R_G):** 100 Ω (1% tolerance)

**Why gain is needed in this application:**
EEG signals are extremely small (tens of microvolts), far below the resolution of typical ADCs.  
- Without amplification, the signal would be lost in quantization noise and electrical interference.  
- Gain stretches these tiny voltages into a range the ADC can measure accurately.  
- Proper gain also ensures the EEG uses a healthy portion of the ADC’s dynamic range, improving resolution.  
- Too little gain = poor resolution (signal looks flat and noisy).  
- Too much gain = clipping on artifacts (flat-topped signals).  

### 3.2 PCB Design
- Layout strategy (analog/digital separation, ground plane).  
- Power supply & regulation (LDO, LiPo, charger).  
- Test points & debugging considerations.  
- EMI/ESD mitigation notes.  

### 3.3 Bill of Materials (BOM)
| Part | Value | Package | Supplier | Notes |  
|------|-------|---------|----------|-------|  
| INA333 | Instrumentation Amp | SOIC-8 | TI/Digikey | Low noise front-end |  
| ESP32-WROOM | MCU + BLE | Module | Espressif | Wireless comms |  
| TPS7A LDO | 3.3V regulator | SOT-223 | TI | Low noise power |  

---

## 4. Firmware (ESP32)
### 4.1 Sampling & Filtering
- ADC configuration (resolution, channels).  
- Sampling rate control (timers/interrupts).  
- Digital filtering implementation (IIR/FIR).  

### 4.2 BLE Data Transmission
- GATT service design (UUIDs, MTU size, packet format).  
- Packetization scheme (data per notification).  
- Error handling/retransmission strategies.  

### 4.3 Power & Reliability
- Sleep modes, watchdogs.  
- Buffering strategy to avoid data loss.  

---

## 5. Software (Python Test Bench)
### 5.1 Connectivity
- BLE client library used (e.g., `bleak`).  
- Connection logic, re-connection strategies.  

### 5.2 Visualization
- Live signal plotting (`pyqtgraph`/`matplotlib`).  
- Filters applied in software.  
- Multiple channels support (EEG, EMG).  

### 5.3 Data Logging & Export
- CSV format definition.  
- Metadata stored (timestamp, channel info, sample rate).  

### 5.4 Automated Tests
- Noise floor measurement.  
- Dynamic range test.  
- FFT analysis (bandwidth verification).  

---

## 6. Compliance & Safety
- Isolation strategy.  
- EMI/ESD considerations.  
- Power management design.  
- DVT checklist (design verification testing).  

---

## 7. Results & Validation
- **Prototype photos & oscilloscope traces.**  
- Noise floor measured vs. target.  
- BLE throughput achieved vs. expected.  
- Python test suite screenshots.  

---

## 8. Lessons Learned
- Analog vs digital noise challenges.  
- BLE reliability tradeoffs.  
- PCB layout issues and fixes.  
- Improvements for next revision.  

---

## 9. References
- Datasheets (INA333, ESP32, regulators).  
- Research papers/EEG-EMG references.  
- Tutorials and design guides.  

---

## 10. Appendices
- Full schematics.  
- PCB layout snapshots.  
- Firmware source code excerpts.  
- Python scripts (main modules).  
