# Kommando-Reference — VoltEdge MVP

> **Formål:** Alle kommandoer du skal bruge, forklaret trin for trin.

---

## 1. Opsætning

### `pip install -r src/requirements.txt`
**Hvad:** Installerer alle Python-pakker (FastAPI, scikit-learn, pytest osv.)  
**Kør:** Én gang efter `git clone` eller når `requirements.txt` ændres

### `python -m venv venv`
**Hvad:** Opretter et virtuelt miljø (isolerer dependencies)  
**Kør:** Én gang per projekt

### `.\venv\Scripts\Activate`
**Hvad:** Aktiverer det virtuelle miljø (Windows)  
**Kør:** Hver gang du åbner en ny terminal

---

## 2. Kørsel

### `cd src && uvicorn main:app --reload --port 8000`
**Hvad:** Starter FastAPI server lokalt  
- `cd src` — går ind i src mappen  
- `uvicorn main:app` — starter serveren med `app` fra `main.py`  
- `--reload` — genstarter automatisk ved filændringer  
- `--port 8000` — kør på port 8000

---

## 3. Test

### `pytest tests/ -v`
**Hvad:** Kører alle tests  
- `tests/` — test-mappen  
- `-v` — verboso (viser hver test navn)

### `pytest tests/test_session_service.py -v`
**Hvad:** Kører kun session tests

---

## 4. Git

### `git init`
**Hvad:** Gør mappen til et git repository

### `git add .`
**Hvad:** Tilføjer alle ændrede filer til staging (klar til commit)

### `git commit -m "besked"`
**Hvad:** Gemmer ændringerne lokalt med en besked

### `git remote add origin <url>`
**Hvad:** Forbinder lokalt repo til GitHub

### `git branch -M main`
**Hvad:** Omdøber branch til `main`

### `git push -u origin main`
**Hvad:** Pusher til GitHub første gang  
- `-u` — husk remote, så næste gang er det bare `git push`

### `git pull --rebase`
**Hvad:** Henter ændringer fra GitHub og lægger dine commits ovenpå

### `git push`
**Hvad:** Pusher commits til GitHub (når remote allerede er sat)

### `git status`
**Hvad:** Viser hvilke filer der er ændret, tilføjet eller klar til commit

---

## 5. Azure Deployment

### Startup Command
```
cd src && uvicorn main:app --host 0.0.0.0 --port 8000
```
**Hvad:** Sættes i Azure Portal → Configuration → General Settings  
Fortæller Azure hvilken kommando der starter app'en

---

## 6. API Test

### Health check
```bash
curl https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/health
```
**Hvad:** Tjekker om app'en kører

### Swagger UI
Åbn i browser:  
`https://voltedge-app-fqgdacaadyd9axds.germanywestcentral-01.azurewebsites.net/docs`

### Postman
1. Åbn Postman
2. **File → Import**
3. Vælg `postman/VoltEdge MVP.postman_collection.json`
4. Sæt `base_url` variablen
5. Kør requests i rækkefølge
