# VoltEdge Mobility A/S — MVP Solution

Welcome to the VoltEdge Mobility A/S MVP solution.  
This project demonstrates a **fully traceable data flow** from telemetry to invoice through an event-driven microservice architecture.

## Happy Path (4 steps)

```
SessionStarted → SessionValidated → SessionRated → InvoiceLineCreated
```

## Architecture

All 3 services run in a **single Azure Web App** and are accessible via URL prefixes:

| Service | Type | URL prefix | Responsibility |
|---|---|---|---|
| **session-service** | Core | `/sessions/*` | ChargingSession aggregate + state machine |
| **billing-service** | Generic | `/billing/*` | Tariff rating + invoice line generation |
| **analytics-service** | Supporting | `/analytics/*` | ML anomaly detection (linear regression) |

**Azure Web App (live):**  
[https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net](https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net)

👉 **Swagger UI:** [https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/docs](https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/docs)

## Tech Stack

- **API:** Python (FastAPI) with Swagger/OpenAPI docs
- **Database:** PostgreSQL (Azure Flexible Server — optional, services fall back to in-memory)
- **Cloud:** Microsoft Azure (App Service) — code-based deployment
- **CI/CD:** GitHub Actions — automatic build, test, deploy and rollback
- **ML:** Scikit-learn Linear Regression (domain service)
- **Secrets:** `.env.example` + GitHub Secrets

## Getting Started (Local Development)

### Setup environment

```bash
python -m venv venv
.\venv\Scripts\Activate  # Windows
# source venv/bin/activate  # Mac/Linux
```

### Option A — Run all services together (recommended)

```bash
cd src
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

👉 Swagger UI: [http://localhost:8000/docs](http://localhost:8000/docs)

### Option B — Run services individually

Each service can also run standalone (e.g., for development):

```bash
# Terminal 1: session-service
cd src/session_service
pip install -r requirements.txt
uvicorn session_api:app --reload --port 8000

# Terminal 2: billing-service
cd src/billing_service
pip install -r requirements.txt
uvicorn billing_api:app --reload --port 8001

# Terminal 3: analytics-service
cd src/analytics_service
pip install -r requirements.txt
uvicorn analytics_api:app --reload --port 8002
```

## Test the Full Flow with curl

### Step 1 — Start session
```bash
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/sessions/start \
  -H "Content-Type: application/json" \
  -d '{"charger_id": "charger-1", "contract_id": "contract-1"}'
```

### Step 2 — Authorize → Start charging → Complete
```bash
# Authorize
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/sessions/{SESSION_ID}/authorize

# Start charging
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/sessions/{SESSION_ID}/start-charging

# Complete (emit SessionValidated)
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/sessions/{SESSION_ID}/complete \
  -H "Content-Type: application/json" \
  -d '{"energy_delivered": 25.5, "duration_minutes": 60}'
```

### Step 3 — Rate session (Billing)
```bash
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/billing/rate \
  -H "Content-Type: application/json" \
  -d '{"session_id": "{SESSION_ID}", "energy_delivered": 25.5, "duration_minutes": 60, "charger_id": "charger-1", "contract_id": "contract-1"}'
```

### Step 4 — Create invoice (Billing)
```bash
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/billing/invoice \
  -H "Content-Type: application/json" \
  -d '{"session_id": "{SESSION_ID}", "total_cost": 92.50, "currency": "DKK", "breakdown": {"energy": 62.50, "parking": 30.0}}'
```

### ML — Anomaly detection (Analytics)
```bash
# Predict expected kWh
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/analytics/predict \
  -H "Content-Type: application/json" \
  -d '{"duration_minutes": 60}'

# Detect anomaly
curl -X POST https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/analytics/detect \
  -H "Content-Type: application/json" \
  -d '{"session_id": "test-1", "energy_delivered": 2.0, "duration_minutes": 60}'
```

## Testing with Postman

1. Open Postman
2. **File → Import** → select `postman/VoltEdge MVP.postman_collection.json`
3. Set the `base_url` variable to your Azure URL or `http://localhost:8000`
4. Run requests in sequence (each step depends on the previous)

The collection includes 12 requests: Health checks, Start, Authorize, Start Charging, Complete, Rate, Invoice, ML Predict, ML Detect — all with test scripts.

## Run Unit Tests

```bash
python -m pytest tests/ -v
```

All 19 tests across 3 services:
- `tests/test_session_service.py` (6 tests)
- `tests/test_billing_service.py` (6 tests)
- `tests/test_analytics_service.py` (7 tests)

## Database

PostgreSQL is **optional** — all services work with in-memory storage by default.

### Automatic schema creation
If the `DATABASE_URL` environment variable is set, the app automatically creates tables on startup via `init_db()` in `session_api.py`.

## CI/CD Pipeline

GitHub Actions workflow (`.github/workflows/`):

### Workflow triggers
- On push to `main` branch
- Manual trigger via `workflow_dispatch`

### Build job
1. **Checkout** source code
2. **Python 3.12** setup
3. **Install dependencies** from `src/requirements.txt`
4. **Upload artifact** for deployment

### Deploy job
1. **Download artifact** from build job
2. **Deploy to Azure Web App** using publish profile credentials

### Rollback
If the deployment fails, the previous version remains untouched on Azure.

## Project Structure

```
├── src/
│   ├── main.py                   # Combined FastAPI app (entry point)
│   ├── requirements.txt          # Combined dependencies for all services
│   ├── session_service/          # Core — ChargingSession aggregate
│   │   ├── session_api.py        # FastAPI endpoints + state machine
│   │   ├── .env.example
│   │   └── __init__.py
│   ├── billing_service/          # Generic — Tariff & Invoice
│   │   ├── billing_api.py        # Rating + invoice endpoints
│   │   ├── .env.example
│   │   └── __init__.py
│   ├── analytics_service/        # Supporting — ML anomaly detection
│   │   ├── analytics_api.py      # Linear regression model + endpoints
│   │   ├── .env.example
│   │   └── __init__.py
│   └── shared/
│       ├── events.py             # Shared event models
│       └── __init__.py
├── tests/
│   ├── test_session_service.py
│   ├── test_billing_service.py
│   └── test_analytics_service.py
├── postman/
│   └── VoltEdge MVP.postman_collection.json
├── .github/workflows/
│   └── main_voltedge-app.yml     # Azure auto-generated deployment workflow
├── requirements.txt              # Root requirements (references src/)
├── MVP.md                        # MVP definition (do not modify)
└── README.md
```

## Secrets Management

- `src/*/.env.example` — templates for local environment variables
- GitHub Secrets: publish profile credentials configured via Azure Deployment Center
- No secrets in source code — only `.env.example` templates

## License

This project is developed as part of the 6th semester exam at Københavns Erhvervsakademi.
