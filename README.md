# Student Transportation Agent AI

A student route-planning dashboard built with React, Vite, FastAPI, and deterministic Python planning. The application works without an AI key or live transit feed. Included fares and schedules are explicitly illustrative and must not be treated as real local service information.

## Requirements

- Windows 10 or 11
- Python 3.10 or later
- Node.js 18 or later (npm is included)
- Internet access for package installation and OpenStreetMap tiles

## Install

Open PowerShell in the `student-travel-agent` project folder:

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
cd ..\frontend
npm install
```

If PowerShell blocks virtual-environment activation, run `\.venv\Scripts\python.exe -m pip install -r requirements.txt` from `backend` and use that same Python executable in the backend start command below. Activation is only a convenience.

## Run The App

Open two PowerShell terminals in the project folder.

Terminal 1, start FastAPI:

```powershell
cd backend
.\.venv\Scripts\Activate.ps1
python -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

Terminal 2, start Vite:

```powershell
cd frontend
npm run dev
```

Open [http://127.0.0.1:5173](http://127.0.0.1:5173). The API health check and interactive API documentation are at [http://127.0.0.1:8000](http://127.0.0.1:8000) and [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs).

The form is prefilled with the Erode demonstration request: College to Railway Station, 8:00 AM departure, 9:00 AM arrival deadline, ₹100 budget, Bus or Train, Balanced. It recommends the illustrative Bus option. Taxi and Walking are rejected because they were not selected. A train is not invented; no train sample service is present. Verification is `UNKNOWN` for sample route data, which is clearly labeled in the results.

## Optional Services

The planner does not need external service credentials. To enable Gemini interpretation and comparison explanations, copy `backend/.env.example` to `backend/.env`, add a Gemini API key, and restart FastAPI. The key remains on the backend. Python constraints and ranking remain authoritative; Gemini output cannot change them. Without a key, the app uses deterministic explanations and `/interpret` reports that AI is unavailable.

The public OSRM driving router is used only when an API request supplies both `starting_coordinates` and `destination_coordinates` as `[latitude, longitude]`. It can enrich the Car/Taxi sample with road geometry and a duration estimate. Fare and live traffic remain unverified. Without coordinates or if OSRM is unavailable, the illustrative estimate remains. The map draws only returned route geometry and never guesses locations. Bus/train schedules are not connected to a live transit feed.

## API

- `GET /` reports backend health and data mode.
- `POST /analyze` validates and summarizes structured requirements.
- `POST /compare` returns the available options and their advantages.
- `POST /check-constraints` returns PASS, FAIL, or UNKNOWN for each check.
- `POST /plan` returns the recommendation, rejected-option explanations, alternatives, and arrival buffer.
- `POST /interpret` optionally interprets a natural-language request with Gemini; it is advisory only.

## Tests And Build

From the project folder:

```powershell
cd backend
python -m pytest -q
cd ..\frontend
npm run build
```

Tests cover request validation, mode restrictions, budget and deadline constraints, all four ranking strategies, no eligible options, the example API workflow, and operation without Gemini credentials.