# Emergency Management System for Large Events

An AI-assisted emergency operations platform for concerts, festivals, sports venues, and other high-density events.

It combines FastAPI services, camera/sensor analysis, incident workflows, and dashboard views to help teams detect risks early, coordinate response resources, and monitor venue safety in real time.

## Features

- Multi-domain emergency handling: `medical`, `fire`, and `security`
- Event lifecycle management with risk metadata
- Incident creation, status tracking, and response workflow endpoints
- AI-assisted analysis endpoints for:
  - Fire detection
  - Crowd density estimation
  - Behavior/threat scoring
- Sensor registry and time-series sensor readings API
- Resource inventory and availability management
- Monitoring endpoints for dashboard/risk/metrics
- SQLite-friendly local mode plus PostgreSQL-ready deployment path
- Docker + Docker Compose setup for API/database/cache stack
- Automated API tests and model behavior tests

## Architecture

### Backend
- FastAPI (`src/api/main.py`) with modular routers
- SQLAlchemy ORM models (`src/data/models.py`)
- Config-driven environment (`config/settings.py`)

### Intelligence Layer
- `src/models/emergency_detector.py` for fire/crowd/behavior/sensor analyzers
- `src/models/response_optimizer.py` for resource and evacuation planning logic
- Lazy model loading in `emergencies_simple` routes for better startup behavior

### Data Layer
- Default local DB: SQLite (`sqlite:///./emergency_management.db`)
- Production-ready path: PostgreSQL + Redis via Docker Compose

### Dashboard / UI Assets
- React dashboard scaffold in `src/dashboard/`
- Standalone HTML dashboards for live/demo use:
  - `web_dashboard.html`
  - `advanced_dashboard.html`

## Tech Stack

| Layer | Technology |
|---|---|
| API | FastAPI, Uvicorn, Pydantic |
| Database | SQLAlchemy, SQLite / PostgreSQL |
| ML / CV | TensorFlow, scikit-learn, OpenCV, NumPy, PyTorch (project deps) |
| Messaging / Cache | Redis, Kafka (configurable) |
| Frontend | React (dashboard scaffold), HTML/CSS/JS dashboards |
| DevOps | Docker, Docker Compose |
| Testing | Pytest, FastAPI TestClient |

## Project Structure

```text
event-management-system/
├── config/
├── data/
│   └── models/
├── docs/
├── fire_detection/
├── notebooks/
├── scripts/
├── src/
│   ├── api/
│   │   ├── main.py
│   │   └── routes/
│   ├── camera/
│   ├── dashboard/
│   ├── data/
│   └── models/
├── tests/
├── run_server.py
├── requirements.txt
├── docker-compose.yml
└── README.md
```

## Quick Start (Local)

### 1. Create environment and install dependencies

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS/Linux
source .venv/bin/activate

pip install -r requirements.txt
```

### 2. Configure environment

```bash
# Windows PowerShell
Copy-Item .env.example .env
# macOS/Linux
cp .env.example .env
```

Recommended local DB for fast setup:

```env
DATABASE_URL=sqlite:///./emergency_management.db
DEBUG=true
```

### 3. Start API (recommended)

```bash
uvicorn src.api.main:app --reload --host 0.0.0.0 --port 8000
```

Open:
- API root: `http://localhost:8000/`
- Health check: `http://localhost:8000/health`
- Swagger docs: `http://localhost:8000/docs`

### 4. Optional demo server

A simplified server with additional demo endpoints is available:

```bash
python run_server.py
```

## Docker Deployment

```bash
docker-compose up -d
```

Services:
- API: `http://localhost:8000`
- PostgreSQL: `localhost:5432`
- Redis: `localhost:6379`

Note: `docker-compose.yml` includes a `dashboard` service reference; ensure a dashboard Dockerfile/build context exists before enabling that service in your environment.

## API Overview

Base prefix: `/api/v1`

### Core routes

- `GET /events/`, `POST /events/`, `GET /events/{id}`, `PUT /events/{id}`, `DELETE /events/{id}`
- `GET /emergencies/`, `POST /emergencies/`, `GET /emergencies/{id}`
- `POST /emergencies/detect/fire`
- `POST /emergencies/analyze/crowd`
- `POST /emergencies/analyze/behavior`
- `POST /emergencies/{id}/response`
- `GET /sensors/`, `POST /sensors/`, `POST /sensors/readings/`, `GET /sensors/{sensor_id}/readings`
- `GET /resources/`, `POST /resources/`, `GET /resources/{id}`, `PUT /resources/{id}`
- `GET /monitoring/dashboard`, `GET /monitoring/risk/{event_id}`, `GET /monitoring/metrics`

Detailed request/response examples: [`docs/API_DOCUMENTATION.md`](docs/API_DOCUMENTATION.md)

## How It Works

1. Events, resources, and sensors are stored in relational tables via SQLAlchemy.
2. Emergency records are created either manually or by AI-assisted endpoint workflows.
3. Detection/analyzer endpoints process image/sensor payloads and return risk signals.
4. Response optimization endpoints generate assignment recommendations and communication plans.
5. Monitoring endpoints aggregate operational status for dashboard consumption.

## Testing

Run all test modules:

```bash
pytest
```

Run focused suites:

```bash
pytest tests/test_api.py
pytest tests/test_emergency_detection.py
```

## Performance Considerations

- Lazy loading for ML components reduces API cold-start overhead.
- SQLite is suitable for local development; use PostgreSQL for concurrency and production durability.
- Heavy ML dependencies can increase startup/memory footprint; deploy model-serving components selectively where needed.

## Security Considerations

- Do not use wildcard CORS in production; restrict trusted origins.
- Rotate and secure `SECRET_KEY`, DB credentials, and external API tokens.
- Add authentication/authorization middleware for production APIs.
- Sanitize and validate file/image payloads before inference.
- Emergency systems are safety-critical; validate outputs with human operators before automated action.

## Current Status

This repository contains both production-intent modules and demo/simulation utilities.

- FastAPI route modules are actively wired in `src/api/main.py`.
- Some advanced flows (for example WebSocket manager integration) are present in source but not currently enabled in the main app.
- Detection endpoints can operate in fallback/mock mode if specific model assets are unavailable.

## Roadmap Suggestions

- Enable authenticated access control and role-based permissions.
- Harden WebSocket/live event streaming and reconnect behavior.
- Add migration-based schema management and seed/version strategy.
- Split model inference into dedicated worker services.
- Add CI pipeline for lint, tests, and container checks.
- Add observability: metrics, traces, and alerting playbooks.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Add tests for your change
4. Submit a pull request with clear technical notes

## Author

**Bhanu Prakash**

- GitHub: `https://github.com/bhanuprakash1708`
- Email: `bhanu.prakash1708@gmail.com`
