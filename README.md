[README.md](https://github.com/user-attachments/files/34856436/README.md)
SENTINEL OS

AI-Powered System Intelligence & Diagnostics Platform

  SENTINEL OS monitors a Windows machine in real time, spots unusual resource behaviour with machine learning, and explains problems in plain language.

> SENTINEL OS is not an operating system. It is a monitoring and diagnostics tool that only *reads* system data. It never kills processes, deletes files, edits the registry, or runs shell commands.

---

 Features

- Live monitoring – CPU (per-core), memory, swap, disk, network and process metrics read through `psutil`, streamed to the UI over WebSocket.
- Process Explorer – searchable, sortable, read-only process table with a detail panel.
- Anomaly detection – an Isolation Forest model flags unusual resource behaviour, with an adjustable sensitivity setting.
- AI System Doctor – a chat-style assistant grounded in measured telemetry. Uses Google Gemini if an API key is set, and falls back to a fully offline rule-based engine otherwise.
- Smart Recommendations – evidence-based, read-only optimisation suggestions.
- Diagnostic History – anomaly events and reports stored in SQLite, with search, filters, and CSV/JSON export.
- Performance Lab – bounded CPU and memory stress experiments (baseline → peak → recovery) plus short-term trend forecasting.

Tech Stack

| Layer | Technology |
| --- | --- |
| Frontend | React, TypeScript, Vite, Tailwind CSS, Recharts |
| Backend | Python, FastAPI, psutil, scikit-learn, pandas |
| Storage | SQLite |
| AI | Google Gemini (optional) + local rule-based fallback |
| Testing | pytest (backend), Vitest + Testing Library (frontend) |

 Quick Start (Windows 10/11)

Requirements: Python 3.11+ and Node.js 18+.

Easiest way: double-click `start.bat`. On the first run it creates a virtual environment, installs dependencies, builds the frontend, starts the monitoring agent, and opens <http://127.0.0.1:8000>.

Manual way (two terminals):

```powershell
# Terminal 1 - backend
cd sentinel-os\backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m uvicorn app.main:app --host 127.0.0.1 --port 8000

# Terminal 2 - frontend
cd sentinel-os\frontend
npm install
npm run dev
```

- App: <http://127.0.0.1:5173>
- API docs (Swagger): <http://127.0.0.1:8000/docs>

> The backend must run on the machine being monitored, since it reads that machine's real performance counters.

 Enabling Gemini (optional)

Everything works without an API key. To turn on Gemini:

1. Copy `backend/.env.example` to `backend/.env`.
2. Set `GEMINI_API_KEY=your_key`.
3. Restart the backend.

The key stays on the backend and is never sent to the browser. If Gemini fails or times out, the Doctor falls back to the offline engine automatically.

 Running Tests

```powershell
# Backend
cd backend
.\.venv\Scripts\python.exe -m pytest

# Frontend
cd frontend
npm run test
```

 Project Structure

```
sentinel-os/
├── backend/
│   ├── app/
│   │   ├── api/         # REST + WebSocket routes
│   │   ├── services/    # monitor, AI doctor, recommendations, forecast, lab
│   │   ├── ml/          # Isolation Forest anomaly engine
│   │   ├── database/    # SQLite layer
│   │   └── schemas/     # Pydantic models
│   └── tests/
├── frontend/
│   └── src/             # pages, components, hooks, services
├── docs/                # ARCHITECTURE, API, PBL report outline, viva Q&A
└── start.bat            # one-click Windows launcher
```

More detail is in [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) and [`docs/API.md`](docs/API.md).

 Safety & Honesty

- Local-first: binds to `127.0.0.1` only.
- Read-only: no destructive actions anywhere in the codebase.
- Real data: every number shown comes from the local monitoring agent, nothing is simulated.
- Not a malware detector: anomaly detection measures unusual resource behaviour, nothing more.
- Bounded experiments: the Performance Lab needs explicit confirmation and enforces server-side limits and timeouts.
- Graceful degradation: metrics that can't be read (e.g. `SYSTEM`-owned processes without admin rights) show as unavailable instead of crashing.

 Troubleshooting

| Problem | Fix |
| --- | --- |
| UI shows *Agent offline* | Start the backend on `127.0.0.1:8000`. |
| AI Doctor says *degraded* | No Gemini key set, so the offline engine is in use. This is normal. |
| `npm` script-execution error in PowerShell | Use `npm.cmd`, or run `Set-ExecutionPolicy -Scope Process Bypass`. |
| Port already in use | Set `$env:SENTINEL_PORT=8010` for the backend. |
