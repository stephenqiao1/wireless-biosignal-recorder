# Wireless Bio-Signal Recorder & Test Bench  
**Author:** Stephen Qiao  
**Location:** Vancouver, Canada  
**Timeline:** May 2025 – August 2025  

---

## 1. Executive Summary
- **Goal:** Brief description of the system (wireless PCB-based biosignal recorder, BLE streaming, Python test suite).  
- **Key Deliverables:** PCB, ESP32 firmware, Python visualization/test bench, compliance-aware design.  
- **Applications:** EMG/EEG-like bio-signal capture for research, prototyping, and test validation.  

---

## 2. System Architecture
- **Block Diagram** (AFE → ADC/ESP32 → BLE → Host).  
- **Signal Path Overview:** Sensor → Amplifier → Filter → ADC → Wireless → Desktop.  
- **Requirements:**  
  - Sampling rate: 250–500 Hz  
  - Bandwidth: 0.5–200 Hz  
  - Noise floor target: < x µV RMS  
  - BLE throughput requirements  

---

## 3. Hardware Design
### 3.1 Analog Front-End (AFE)
- Instrumentation amplifier choice (e.g., INA333/AD620).  
- Filtering stages (high-pass, low-pass, notch).  
- Gain calculations.  
- Protection (ESD diodes, ferrite beads, resistors).  

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
