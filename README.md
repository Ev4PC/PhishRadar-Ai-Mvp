# PhishRadar AI — MVP

> Analizador educativo de **phishing** para **emails y URLs**. Genera un **score 0–100**, etiqueta el resultado (**phishing / legit**), explica **motivos** y permite **exportar informe PDF** con logo y desglose técnico.  
> Stack: **FastAPI + Python** (backend) · **React + Vite** (frontend) · **ReportLab** (PDF).  

---

## ✨ Funcionalidades

- **Análisis de Emails** (pega RAW RFC822 o contenido `.eml`)
- **Análisis de URLs** (sin visitar, extracción de patrones y features)
- **Score + Etiqueta**: 0–100 + `phishing` / `legit` (umbral configurable)
- **Top motivos**: señales activadas y su peso
- **Consejos** de mitigación
- **Exportación a PDF** con logo, cabecera, motivos, consejos y tabla de features
- **CORS** abierto por defecto para `localhost` (dev)
- **Docker** (opcional) y estructura lista para CI/CD

---

## 🧱 Arquitectura

phishradar-ai-mvp/
├─ backend/
│ ├─ app.py # FastAPI (endpoints)
│ ├─ phishradar_core/
│ │ ├─ features/ # Extractores de features (email, url, util)
│ │ ├─ model/ # Reglas y scoring baseline
│ │ └─ report/ # Render del PDF (ReportLab)
│ └─ requirements.txt
└─ frontend/
├─ src/components/ # UI (React)
├─ vite.config.js
└─ package.json


---

## 🚀 Demo rápida (local)

- **API**: `http://localhost:8000/health` → `{"status":"ok"}`
- **UI**: `http://localhost:5173`

---

## ⚙️ Puesta en marcha

### 1) Backend (FastAPI)

```bash
cd backend
python -m venv .venv && source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# arrancar API
uvicorn app:app --reload --port 8000

cd frontend
npm install
npm run dev    # abre http://localhost:5173
#El botón Export PDF llama al endpoint /report/email o /report/url

🔌 Endpoints (API)

GET /health → estado

POST /analyze/email → { raw: string } → score + motivos + consejos + features

POST /analyze/url → { url: string } → score + motivos + consejos + features

POST /report/email → PDF (stream)

POST /report/url → PDF (stream)

Ejemplo:

curl -X POST http://localhost:8000/analyze/url \
  -H "Content-Type: application/json" \
  -d '{"url": "http://bit.ly/3xyz"}'

🧠 Scoring (baseline)

El motor aplica reglas con pesos (cabeceras, URL, contenido) y suma hasta 100.
Umbral por defecto: ≥ 55 → phishing (configurable en phishradar_core/model/baseline.py).

Ejemplos de señales:

Cabecera: spf_dkim_fail, from_replyto_mismatch, msgid_domain_mismatch, display_name_spoof

URL: shortener_used, ip_in_url, http_not_https, links_total_*, http_links_ratio_high

Contenido: urgency_keywords, parcel_keywords, customs_keywords, deadline_24h, blacklist_threat

Adjuntos peligrosos: dangerous_attachment

🧪 Datos de prueba (opcional)

Coloca ejemplos en backend/data/ si quieres versionarlos (sin datos sensibles).
Para GIF/vídeo demo, guárdalo en la raíz del repo como demo.gif y usa:

![Demo](./demo.gif)

📄 Licencia

MIT — ver LICENSE

🤝 Desarrollado por 
Autor: Ev4PC