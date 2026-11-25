# Ward Desk – Patient Admission Management

A web app for hospital staff to admit, search, filter, edit, discharge and delete patients.
Built for the DScribe Software Engineer Intern Round 1 task.

**Live link:** `<PASTE YOUR LIVE URL HERE>`
**Repo:** `<PASTE YOUR GITHUB URL HERE>`

## Tech stack

| Layer | Choice |
|---|---|
| Frontend | React 18 (Vite), plain CSS, responsive for mobile / tablet / desktop |
| Backend | Python 3 + Flask (REST API), gunicorn in production |
| Database | SQLite (file on disk, so data survives server restarts) |

## Features

- Add, view, edit and delete patients (delete asks for confirmation)
- Mark a patient as **Discharged** (one click)
- Search by name; filter by Ward, Department and Doctor (filters combine)
- Validation in **both** frontend and backend; the backend returns clear messages, shown next to the field in the UI
  - Name: required, letters only (spaces between words allowed)
  - Age: required, whole number 0–120
  - Gender, Ward, Department, Doctor: required, must be from the list
  - Admission date: required, cannot be in the future (checked in IST on the server)

## Project structure

```
backend/   app.py (API + validation), test_app.py, requirements.txt
frontend/  src/ (App, components, api.js, validation.js, styles.css)
```

## API

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/options` | Dropdown values |
| GET | `/api/patients?search=&ward=&department=&doctor=` | List / search / filter |
| POST | `/api/patients` | Add patient |
| PUT | `/api/patients/<id>` | Edit patient |
| PATCH | `/api/patients/<id>/discharge` | Mark discharged |
| DELETE | `/api/patients/<id>` | Delete patient |

Invalid data returns `400` with `{ "error": "<first message>", "errors": { "<field>": "<message>" } }`.

## Run locally

Requires Python 3.10+ and Node 18+.

```bash
# 1. Backend  (http://localhost:5000)
cd backend
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python app.py

# 2. Frontend (new terminal, http://localhost:5173)
cd frontend
npm install
npm run dev
```

The Vite dev server proxies `/api` to the Flask server, so no extra config is needed.

Run backend tests: `cd backend && python -m unittest test_app.py -v`

## Deploy (single service: Flask serves the built React app)

Flask serves `frontend/dist` and `/api` from the same URL, so frontend and backend are always connected.

### Option A – PythonAnywhere (free, files persist)
1. `cd frontend && npm install && npm run build`, then commit the `frontend/dist` folder and push.
2. On pythonanywhere.com: **Consoles → Bash**: `git clone <repo>`, then
   `cd <repo>/backend && python3 -m venv ~/venv && ~/venv/bin/pip install -r requirements.txt`.
3. **Web → Add a new web app → Manual configuration**, set *Virtualenv* to `/home/<user>/venv`.
4. Edit the WSGI file so it contains only:
   ```python
   import sys
   sys.path.insert(0, "/home/<user>/<repo>/backend")
   from app import app as application
   ```
5. Click **Reload**. Your live link is `https://<user>.pythonanywhere.com`.

### Option B – Render (paid plan; persistent disk required for SQLite)
`render.yaml` is included: create a new **Blueprint** from the repo.

## Screenshots

Add screenshots to `docs/` and link them here (desktop list, mobile list, add form with validation errors, delete confirmation).

![Desktop](docs/desktop.png)
![Mobile](docs/mobile.png)
