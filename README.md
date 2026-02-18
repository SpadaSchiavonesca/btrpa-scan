# btrpa-scan (GRC Enhancement)
**Developed by: Nenad Uzelac**

This repository is a GRC-focused fork of `btrpa-scan`. I implemented a **Risk Scoring Engine** to automate the analysis of Bluetooth environments based on **NIST SP 800-121 Rev 2** standards.

## 📊 Project Features
* **Automated Risk Triage:** Instantly flags "Critical" threats (like hidden cameras) using keyword detection.
* **Proximity Analysis:** Calculates risk based on Signal Strength (RSSI).
* **Compliance Reporting:** Generates structured Markdown reports for audit documentation.

## 📸 Technical Demo

### 1. Command Line Interface (CLI)
The tool is built with a professional CLI, allowing auditors to easily specify scan data and output paths.
![CLI Help](btrpa-help.png)

### 2. Live Risk Assessment
This shows the engine detecting a "Hidden Spy Cam" and assigning a **CRITICAL** risk score in real-time.
![Risk Analysis Result](btrpa-risk-analysis.png)

---
*Note: This project was developed as part of my learning path in GRC and Cybersecurity.*
