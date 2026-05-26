# Fremdriftslog — VoltEdge MVP

> **Formål:** Kronologisk log over alt hvad vi har lavet, så vi altid kan se hvor vi er.

---

## 26-05-2026

### ✅ Step 1 — Azure Web App oprettet
- **Navn:** `voltedge-app`
- **URL:** `https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net`
- **Runtime:** Python 3.12, Linux, Free F1
- **Resource Group:** VoltEdge
- **Region:** Germany West Central

### ✅ Step 1b — VoltEdge mappe oprettet
- Mappen `VoltEdge/` oprettet i `Eksamen/`
- `MVP.md`, `README.md`, `liste.md` flyttet ind

### ✅ Step 1c — README.md genskabt
- Indeholder: projektinfo, arkitektur, setup-guide, curl-test, CI/CD beskrivelse

### ✅ Step 2 — Git opsat
- `.gitignore` oprettet (`venv/`, `__pycache__/`, `.env`, `.opencode/`)
- `git init` kørt lokalt

### ✅ Step 3 — Python app oprettet
- **`src/main.py`** — FastAPI entry point
- **`src/session_service/session_api.py`** — ChargingSession state machine (6 endpoints)
- **`src/billing_service/billing_api.py`** — Tariff rating + invoice (3 endpoints)
- **`src/analytics_service/analytics_api.py`** — ML anomaly detection (3 endpoints)
- **`src/shared/events.py`** — Fælles event-modeller
- **`src/requirements.txt`** — Dependencies (FastAPI, scikit-learn, pytest)
- **`.env.example`** — Miljøvariabel skabeloner (3 stk)
- **`__init__.py`** — Python packages (5 stk)

### ✅ Step 3b — Kode-reference oprettet
- `docs/kode_reference.md` — forklarer hver fil og endpoint

### ✅ Step 4 — Git commit + push til GitHub
- **GitHub:** https://github.com/Lula0002/VoltEdge
- **Branch:** `main`
- **Antal filer:** 20 stk

### ✅ Step 5 — Azure Deployment Center forbundet
- **Source:** GitHub → `Lula0002/VoltEdge` → `main`
- **Authentication:** Basic auth + SCM aktiveret
- **Workflow:** "Add a workflow" valgt

### ✅ Step 6 — Startup command sat
- **Kommando:** `cd src && uvicorn main:app --host 0.0.0.0 --port 8000`

### ✅ Step 7 — Deployment SUCCES!
- **/health:** `{"status":"healthy","app":"voltige-mvp","version":"1.0.0"}`
- **Swagger:** `/docs` — alle 12 endpoints synlige
- **CI/CD pipeline:** GitHub → Azure virker

### ✅ Step 8 — Tests + Postman oprettet
- **`tests/test_session_service.py`** — 6 tests (session state machine)
- **`tests/test_billing_service.py`** — 6 tests (prisberegning)
- **`tests/test_analytics_service.py`** — 7 tests (ML anomaly)
- **`postman/VoltEdge MVP.postman_collection.json`** — 12 requests i 4 grupper

---

## Næste skridt (Pending)

- [ ] Kør `pip install -r requirements.txt` lokalt
- [ ] Kør `pytest tests/ -v` for at teste
- [ ] Step 9: Database (PostgreSQL)
- [ ] **Step 10: Slet tests/ og postman/ før aflevering**

---

## Noter
- **MVP.md må ikke redigeres** uden eksplicit tilladelse
- **Ingen hardcoding** — miljøvariabler styrer al konfiguration
- **Ingen Docker** — ren Python/zip deploy
- **Kun mig (dig) må pushe** til GitHub
