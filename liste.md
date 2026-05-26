# Step-by-step plan — VoltEdge MVP

> **Regel:** Kun mig (dig) der pusher og deployer. Jeg guider ét skridt ad gangen.

## Steps

- [x] **Step 1:** Opsæt Azure Web App (basis)
- [x] **Step 1b:** Opret VoltEdge mappe + flyt filer
- [x] **Step 1c:** Genskab README.md
- [x] **Step 2:** Opret `.gitignore` + initialiser git lokalt
- [x] **Step 3:** Opret Python-app (`src/` struktur + main.py + 3 services)
- [x] **Step 3b:** Opret `docs/kode_reference.md`
- [x] **Step 4:** Commit + push til GitHub (`main` branch)
- [x] **Step 5:** Forbind Azure Web App til GitHub (deployment source)
- [x] **Step 6:** Sæt startup command i Web App
- [x] **Step 7:** **Deployment SUCCES — /health virker!** ✅
- [x] **Step 8a:** Opret tests (19 tests)
- [x] **Step 8b:** Opret Postman collection (12 requests)
- [x] **Step 8c:** Opret `docs/log.md` — fremdriftslog
- [x] **Step 8d:** README.md oversat til engelsk
- [ ] **Step 9:** Opsæt database (PostgreSQL)
- [ ] **Step 10:** 🔥 SLET før aflevering:
  - `tests/` mappe
  - `postman/` mappe
  - `docs/` mappe
  - `liste.md`

> **Påmindelse:** Step 10 sletter tests/, postman/, docs/ og liste.md før aflevering.
> Kun kode, README.md, MVP.md og .github/workflows bliver tilbage.
