# System Architecture Document — PulseFit AI

**Project Name:** PulseFit AI  
**Author:** Nachiket Gadilohar  

---

## 1. Architecture Overview

```mermaid
graph TB
    Wearables["Wearables / CSV Log"] --> Ingestion["FastAPI Telemetry Ingestion"]
    Ingestion --> ML["Scikit-Learn Analytics & Recovery Calculator"]
    ML --> Agent["PulseFit Autonomous LLM Coach"]
    Agent --> UI["Streamlit Analytics Dashboard"]
```

### Component Details:
- **Backend API**: FastAPI, Python 3.10.
- **Analytics & ML**: Pandas, NumPy, Scikit-Learn, SciPy.
- **AI Agent**: LangChain / OpenAI GPT-4o.
- **Frontend**: Streamlit / React UI.
