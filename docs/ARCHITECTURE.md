# System Architecture Document — PulseFit AI

**Project Name:** PulseFit AI  
**Author:** Nachiket Gadilohar  

---

## 1. Architecture Diagram

```mermaid
graph TB
    Wearables["Biometric Data (CSV / API)"] --> Ingestion["FastAPI Data Ingestion"]
    Ingestion --> Analytics["Scikit-Learn Analytics Engine"]
    Analytics --> Agent["PulseFit LLM Coach Agent"]
    Agent --> UI["Streamlit / React Dashboard"]
```
