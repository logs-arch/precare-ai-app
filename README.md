# PreCare - AI-Powered Maternal Health Companion


**PreCare** is a comprehensive maternal health platform combining AI-driven risk assessment, autonomous agentic workflows, real-time health tracking, emergency response orchestration, and intelligent conversational support (Maya AI). Designed for pregnant women, healthcare providers, and emergency responders.

## 🚀 Features

### Core Capabilities
- **Pregnancy Report Analysis**: OCR extraction from PDFs/images → Structured data parsing (Hb, BP, protein) → AI risk classification (FINE/MODERATE/DANGER)
- **Autonomous Agent Workflows**: LangGraph-powered agents that automatically:
  - Schedule nearest doctor appointments for moderate risks
  - Trigger emergency protocols (ambulance + doctor + family alerts) for critical cases
  - Request user confirmation for routine actions
- **Maya AI Chatbot**: Real-time conversational health advisor with risk monitoring
- **Digital Twin**: Predictive health modeling based on historical trends
- **Health Tracking**: Hb, BP, glucose, weight monitoring with status indicators
- **Emergency Management**: One-tap SOS with location-aware response orchestration
- **Role-Based Dashboards**: Patient / Doctor / Emergency Responder / Staff views

### Backend AI Pipeline
```
Upload Report → OCR Extract → Risk Classify → Agent Plan → Auto-Execute → Digital Twin Update
```

## 🏗️ Architecture

```
Frontend (SwiftUI iOS)
    ↕️ REST + WebSocket (APIClient.swift)
Backend (FastAPI Python)
    ↕️ SQLite ORM + LangChain/LangGraph
    ↕️ Claude-3.5 / Gemini / OpenAI LLMs
    ↕️ Tesseract OCR + PyPDF
```

**Tech Stack**:
- **Backend**: FastAPI, SQLAlchemy, Pydantic, LangGraph (agentic workflows), LangChain, OpenAI/Anthropic APIs
- **Frontend**: SwiftUI, NavigationStack + AppRouter, Combine for state
- **Database**: SQLite (`precare.db`)
- **OCR**: pytesseract + Pillow + pypdf
- **Deployment**: Codemagic CI/CD for iOS

## 📁 Project Structure

```
PreCare-App/
├── precare__backend-main/          # FastAPI Backend
│   ├── app/                       # Core app (main.py, db.py, models.py, schemas.py)
│   ├── routers/                   # API endpoints (auth.py, reports.py, ai.py, agent.py...)
│   ├── services/                  # Business logic (agentic_service.py, ocr_service.py...)
│   ├── uploads/                   # User-uploaded reports (PDFs/images)
│   ├── assets/                    # Screenshots + demo images
│   ├── precare.db                 # SQLite database
│   └── requirements.txt           # Python deps
└── PreCare-main/                  # SwiftUI iOS App
    ├── PreCare/                   # Main app target
    │   ├── Features/              # Auth, Dashboard, Analysis, Emergency...
    │   ├── Core/                  # Networking (APIClient.swift), Storage, Routing
    │   └── Shared/Components      # Reusable UI (Cards, Buttons...)
    ├── PreCare.xcodeproj
    └── codemagic.yaml             # iOS CI/CD
```

## 🚀 Quick Start

### Backend Setup & Run
```bash
cd precare__backend-main

# Install dependencies
pip install -r requirements.txt

# Copy & configure .env
cp .env.example .env
# Edit .env: Add OPENAI_API_KEY, GOOGLE_GEMINI_API_KEY, ANTHROPIC_API_KEY

# Run FastAPI server
uvicorn app.main:app --reload --port 8000
```

**Backend API Docs**: http://localhost:8000/docs

### iOS App Setup & Run
```bash
cd PreCare-main

# Open Xcode project
open PreCare.xcodeproj

# Backend must be running at http://127.0.0.1:8000
# Update APIClient.swift baseURL if needed
# Build & Run (iOS 16+)
```

**Codemagic CI/CD**:
```bash
# Triggers automatic iOS builds/IPA generation
# Configured in codemagic.yaml (ad-hoc distribution)
```

## 🔌 API Endpoints

| Endpoint | Method | Description | Auth |
|----------|--------|-------------|------|
| `/health` | GET | Health check | - |
| `/auth/signup` | POST | User registration | - |
| `/auth/login` | POST | JWT login | - |
| `/auth/me` | GET | User profile | ✅ |
| `/reports/upload` | POST | Upload + OCR + Auto-agent | ✅ |
| `/reports/{id}` | GET | Get report details | ✅ |
| `/ai/analyze-risk` | POST | Re-analyze report | ✅ |
| `/agent/plan` | POST | Get agent plan | ✅ |
| `/agent/confirm` | POST | Confirm/decline actions | ✅ |
| `/appointments/book` | POST | Manual appointment | ✅ |
| `/appointments/auto-book` | POST | Auto nearest doctor | ✅ |
| `/emergency/trigger` | POST | Emergency activation | ✅ |
| `/maya/chat` | POST/WS | Ask Maya AI | ✅ |
| `/health-tracking/metrics` | POST | Log vitals | ✅ |
| `/dashboard/{user_id}` | GET | Role-based dashboard | ✅ |

**Full OpenAPI**: `http://localhost:8000/openapi.json`

## 🎯 Key Workflows

### 1. Patient Report Upload (End-to-End)
```
1. Upload prenatal report (PDF/Image)
2. OCR → Extract Hb/BP/Protein
3. Claude AI → Risk: FINE/MODERATE/DANGER (w/ confidence)
4. Agent executes:
   - FINE: "Routine appt?" (user confirm)
   - MODERATE: Auto-book nearest doctor (user confirm)
   - DANGER: Emergency! (ambulance + alerts)
5. Maya tracks ongoing risk via chat
```

### 2. Emergency Response
```
Critical risk → trigger_emergency()
→ Notify family contacts
→ Alert nearest doctor/hospital  
→ Dispatch ambulance (ETA tracking)
→ Real-time status updates
```

### 3. Digital Twin
```
Historical vitals + trends → Predictive modeling
→ Risk forecasts → Proactive alerts
→ Maya: "Your Hb trend suggests monitoring..."
```

## 📊 Database Schema (SQLite)

Key tables via `app/models.py`:
- `User` (patients/doctors): public_id, role (PATIENT/DOCTOR/EMERGENCY)
- `Report`: extracted_text, risk_level, auto_actions JSON
- `Appointment`: doctor_name, hospital, status
- `Emergency`: severity, ambulance_status
- `AgentActionLog`: Audit trail of agent decisions
- `HealthMetric`: Hb, BP, glucose, weight_kg
- `MayaChat`: Conversation history

## 🛠️ Environment Variables

**.env** (backend):
```
OPENAI_API_KEY=sk-...
GOOGLE_GEMINI_API_KEY=...
ANTHROPIC_API_KEY=...
LLM_MODEL=gemini-1.5-flash  # or claude-3-5-sonnet-latest
```

## 📱 Screenshots

See `precare__backend-main/assets/` for iOS app screens:
- Login/Register
- Patient Dashboard (risk cards, upload)
- AI Analysis Results
- Maya Chat
- Emergency Responder View
- Doctor Dashboard (all reports)

## 🔒 Security

- JWT Bearer tokens (refresh/revoke)
- Role-based access (PATIENT/DOCTOR/EMERGENCY/ADMIN)
- User scoping on reports/actions
- File upload validation (type/size)
- SQLAlchemy ORM with session management

## 🤝 Contributing

2. Frontend: Xcode → PreCare.xcodeproj (backend @ localhost:8000)
3. Test API: `curl -X POST http://localhost:8000/docs`
4. Database: `sqlite3 precare.db` or http://localhost:8000/docs → /health

## 📄 License

MIT - See [LICENSE](LICENSE) (add if needed)

---

**Built with ❤️ for maternal health safety**  
*Autonomous AI agents that act before risks become emergencies.*
