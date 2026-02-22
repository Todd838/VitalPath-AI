# VitalPath AI - Real-World Organ Transport Workflow

1. Mission Creation (Dispatch)
2. Route Planning and Dispatch
3. Vehicle Preparation
4. Transport and Real-Time Monitoring
5. Dynamic Adjustments
6. Arrival and Verification
7. Post-Mission Review

## Workflow Detail

### 1. Mission Creation (Dispatch)
Trigger: A hospital identifies an organ ready for transport.

Donor hospital inputs:
- Organ type (heart, kidney, liver, etc.)
- Required delivery timeline
- Pickup location

Backend evaluates max safe transport time and organ-specific constraints:
- Heart: 4 to 6 hours
- Kidney: 24 to 36 hours
- Liver: 8 to 12 hours

Backend decides transport mode:
- Road: if ETA fits the organ max time
- Air: if distances are long or the road ETA exceeds the safe window

VitalPath role:
Automatically generates the mission object (donor, recipient, organ type, transport mode) and stores it in the system.

### 2. Route Planning and Dispatch
Backend retrieves real-time routing data using Google Maps Platform:
- Routes API for routing and ETA
- Places API (New) for search and geocoding

Routing generates:
- Full route coordinates
- Turn-by-turn navigation
- ETA for each leg

System sends route and mission info to the vehicle dispatch team.

VitalPath role:
Ensures the transport team knows the fastest, safest route while respecting organ-specific constraints.

### 3. Vehicle Preparation
Vehicle team checks:
- Organ transport container is pre-cooled
- Sensors operational (temperature, shock, lid, battery)
- GPS/telemetry device installed
- Organ loaded into container

Vehicle is activated in VitalPath to start mission tracking.

VitalPath role:
Monitors vehicle status and container telemetry in real time.

### 4. Transport and Real-Time Monitoring
Vehicle begins journey:
- Dashboard shows live location, ETA, and turn-by-turn navigation

Telemetry streams to backend:
- Temperature
## Setup & Run

# VitalPath AI - Overview

VitalPath AI is an AI-powered safety guardian for life-critical medical shipments (organs, blood, vaccines) that monitors cargo conditions, determines the best route and transport mode, and helps transport teams act quickly if something goes wrong.

## Key Workflow

1. **Mission Creation:** Hospital identifies an organ ready for transport. Backend evaluates constraints and decides transport mode (road/air).
2. **Route Planning:** Backend uses Google Maps Platform (Routes & Places APIs) to generate route, ETA, and navigation. Dispatch team receives route info.
3. **Vehicle Preparation:** Transport container is prepped, sensors checked, vehicle activated in VitalPath for tracking.
4. **Transport & Monitoring:** Vehicle begins journey, dashboard shows live location and telemetry. Backend compares sensor data to safe thresholds, AI evaluates cargo integrity and risk.
5. **Dynamic Adjustments:** Backend recalculates routes for disruptions (traffic, closures, weather), UI updates automatically.
6. **Arrival & Verification:** Arrival time, final telemetry, and alerts logged. AI generates mission summary.
7. **Post-Mission Review:** Operations team reviews logs, routes, and AI recommendations for continuous improvement.

## Core Features

- **Organ-Aware Routing:** Mode and route chosen based on organ type, max safe time, and real-world constraints.
- **Real-Time Simulation:** Map displays route, vehicle moves in real time, navigation and ETA update continuously.
- **Cargo Monitoring:** Real-time sensor data (temperature, shock, lid, battery, elapsed time) shown; AI evaluates viability.
- **Alerts & Risk Assessment:** Alerts triggered for unsafe readings; AI provides recommendations and risk status.
- **AI Assistance:** Users can ask about viability, transport mode, and risk; AI answers based on current context.
- **Mission Log:** All events (alerts, route changes, recommendations, trip start/finish) are logged for review.

## Architecture

- **Frontend:** React + Vite, MapLibre GL for map, panels for mission details, navigation, vitals, hospital info, AI chat, and telemetry. High-contrast dashboard layout.
- **Backend:** FastAPI (Python), Google Routes & Places APIs, Gemini AI, ElevenLabs voice, normalizes responses, manages telemetry, alerts, and mission logs.
- **Algorithmic Race:** Dijkstra vs Duan–Mao BM-SSSP for route computation, visualized in the UI with benchmarks and telemetry.

## Setup & Run

**Prerequisites:** Python 3.10+, Node.js 18+, C/C++ build tools (Windows)

**Backend:**
```bash
cd backend
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

**Frontend:**
```bash
cd frontend
npm install
npm run dev
```
Open http://localhost:5173

**Configuration:**
Copy `.env.example` to `.env` in backend. Add `GEMINI_API_KEY` and `ELEVENLABS_API_KEY` if needed.

**BM-SSSP runner:**
```bash
cd backend/bmssp-runner
npm install
```
Enable BM-SSSP:
```bash
cd backend
VITALPATH_AI_ROUTE_ALGO=bmsssp uvicorn app.main:app --reload --port 8000
```

## API Overview

- `GET /api/algo/geocode?q=...` → `{ lat, lng, display_name }`
- `GET /api/algo/autocomplete?q=...` → `{ results: [{ lat, lng, display_name }, ...] }`
- `POST /api/algo/calculate` with start/end, scenario, algorithm, etc. Returns route, ETA, steps, and optional exploration fields.
- `POST /api/ai/chat` → AI response
- `POST /api/ai/speak` → audio (if configured)

## Features & Demo Highlights

- End-to-end EMS dashboard loop: dispatch, vitals, navigation, live map, AI assistant, seamless rerouting
- Dev Mode: scenario injection, algorithm comparison, roadblock injection, benchmark mode
- Algorithmic Race: Dijkstra vs BM-SSSP, visualized with KPIs, trend lines, histograms
- UI/UX: high-contrast layout, panels, cinematic transitions, air-route visualization
- AI & Voice: Gemini chat, ElevenLabs TTS, local audio fallbacks, AI-triggered dispatch

## Troubleshooting

- Backend not running: Start backend first if you see ECONNREFUSED in frontend
- Google Maps API errors: Check keys, enable APIs, check quotas
- AlgoRace not visible: Dev mode must be enabled and scenario injected

## Data Attribution & Licensing

VitalPath AI uses OpenStreetMap data via OSMnx/Overpass and geocoding via Nominatim. OpenStreetMap data is licensed under ODbL.

---

VitalPath AI is a mission control simulator that combines real-time mapping, AI conversation, voice synthesis, and cargo telemetry to demonstrate how technology can improve organ transport outcomes.

### First scenario route feels slow
OSMnx may be cold-starting (graph download/build cache).
- Run each scenario once beforehand (warm cache), then it’s much faster.

### Google Maps API errors
If routing or search fails, check:
- `GOOGLE_MAPS_SERVER_KEY` in `backend/.env`
- API enablement: **Routes API** + **Places API (New)**
- Quotas / billing on the Google Cloud project

### AlgoRace not visible
AlgoRace is shown when:
- Dev mode is enabled and a scenario is injected
- Both algorithms failing hides the widget automatically

---

## Data attribution & licensing
VitalPath AI uses **OpenStreetMap** data via OSMnx / Overpass and geocoding via Nominatim.
OpenStreetMap data is licensed under **ODbL** — see https://www.openstreetmap.org/copyright.

