# Software Requirements Specification (SRS) — PulseFit AI

**Project Name:** PulseFit AI  
**Author:** Nachiket Gadilohar  

---

## 1. Functional Requirements
- **FR-BIO-01**: System MUST calculate Daily Recovery Index using weighted formula: (Normalized HRV * 0.5) + (Sleep Efficiency * 0.3) + (RHR Baseline Delta * 0.2).
- **FR-AGENT-01**: System MUST output structured JSON workout prescriptions containing exercise name, target RPE, sets, and rep ranges.
- **FR-UI-01**: Dashboard MUST display interactive charts for weekly training volume and muscle group distribution.

---

## 2. Non-Functional Requirements
- **NFR-PERF-01**: Biometric calculation and AI coach response latency MUST NOT exceed 1.5 seconds.
- **NFR-SEC-01**: User health metrics MUST be encrypted at rest using AES-256.
