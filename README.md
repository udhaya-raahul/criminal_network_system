# 🔍 CNAS — Criminal Network Analysis System
### AI-Powered Criminal Intelligence Platform
**Problem Statement #26189 | Ministry of Home Affairs | NCRB**

---

## 🚀 Local setup

### Prerequisites

- Docker Desktop with Docker Compose, for the full database-backed stack.
- Python 3.11, for the backend and its pinned dependencies.
- Node.js 20 or later, for the frontend.

### 1. Configure secrets

Copy the root template to `.env` and replace every `replace-with-...` value with a unique local value. The secret key must be at least 32 characters. Do not commit either `.env` file.

```powershell
Copy-Item .env.example .env
Copy-Item backend/.env.example backend/.env
```

For direct (non-Docker) backend development, set `NEO4J_URI=bolt://localhost:7687` in `backend/.env`. Docker Compose supplies its own container hostnames.

### 2. Start and seed the full stack

```powershell
docker compose up -d --build
cd backend
.\venv\Scripts\python.exe data/seed_demo.py
```

The app is then available at http://localhost:5173 and the API documentation at http://localhost:8000/docs.

### 3. Run without Docker

The backend can run in its offline/demo fallback mode when Neo4j is unavailable, but data is limited. In separate terminals:

```powershell
cd backend
py -3.11 -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

```powershell
cd frontend
npm ci
npm run dev
```

### Verify changes

```powershell
cd frontend
npm run lint
npm run build

cd ..\backend
.\venv\Scripts\python.exe -m unittest discover -s tests -v
```

---

## 🔑 Demo Credentials

| Component | URL | Auth |
|---|---|---|
| **App** | http://localhost:5173 | No auth required |
| **API Docs** | http://localhost:8000/docs | No auth required |
| **Neo4j Browser** | http://localhost:7474 | Values from `.env` |

---

## 🏗 Architecture

```
┌────────────────────────────────────────────┐
│         React + TypeScript Frontend         │
│    (Vite · Tailwind CSS · Cytoscape.js)    │
└─────────────────────┬──────────────────────┘
                      │ REST API
┌─────────────────────▼──────────────────────┐
│           FastAPI Python Backend            │
│   NLP (spaCy) · Analytics (NetworkX)       │
│   Entity Extraction · Pattern Detection    │
└──────┬─────────────────────────┬───────────┘
       │                         │
┌──────▼───────┐         ┌───────▼──────┐
│   Neo4j 5.x  │         │  Redis 7.x   │
│  Graph DB    │         │   Cache      │
└──────────────┘         └──────────────┘
```

## 🎯 Key Features

### 1. Multi-Source Data Ingestion
- Upload FIRs, CDRs, Financial Records, Surveillance Reports
- Supports: PDF, DOCX, XLSX, CSV, TXT, JSON
- Automatic document type detection

### 2. AI-Powered Entity Extraction
- Named Entity Recognition via spaCy (en_core_web_lg)
- Extracts: Persons, Organizations, Locations, Phone Numbers, Vehicles, Bank Accounts, Crypto Wallets, IP Addresses, Social Media Handles
- Confidence scoring and deduplication

### 3. Relationship Mapping
- Automatically infers relationships from co-occurrence
- 12 relationship types: Associate, Financial Link, Communication, Location, etc.
- Evidence-linked relationship creation

### 4. Network Analytics
- **PageRank** — Identify most influential actors
- **Betweenness Centrality** — Find network brokers and intermediaries
- **Community Detection** (Louvain) — Discover criminal cells/groups
- **Risk Scoring** — Automated threat level computation

### 5. AI Intelligence Chat
- Natural language questions about the network
- "Who are the key players?" / "What suspicious patterns exist?"
- Powered by live graph analytics data

### 6. Pattern Detection Alerts
- Star topology detection (hub-and-spoke networks)
- Unusual communication frequency spikes
- Isolated financial clusters
- Cross-network hawala detection

---

## 📦 Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 18, TypeScript, Vite, TailwindCSS |
| **Visualization** | Cytoscape.js (graph), D3-force layout |
| **Backend** | FastAPI, Python 3.11, Uvicorn |
| **NLP** | spaCy 3.7, sentence-transformers, scikit-learn |
| **Graph DB** | Neo4j 5.x (APOC, full-text index) |
| **Analytics** | NetworkX, python-louvain, scipy |
| **Cache** | Redis 7 |
| **Containerization** | Docker, Docker Compose |

---

## 🗂 Project Structure

```
criminal_network_system/
├── backend/
│   ├── app/
│   │   ├── api/routes.py          # All REST endpoints
│   │   ├── core/                  # Config, database
│   │   ├── nlp/                   # Entity extraction, NLP
│   │   ├── graph/                 # Neo4j service layer
│   │   ├── analytics/             # Network analysis engine
│   │   └── models/schemas.py      # Pydantic data models
│   ├── data/seed_demo.py          # 🆕 Demo data seeder
│   └── requirements.txt
├── frontend/
│   └── src/
│       ├── pages/                 # All page components
│       ├── components/Layout/     # Sidebar, Header
│       ├── services/api.ts        # API client
│       └── hooks/useStore.ts      # Zustand state
└── docker-compose.yml
```

---

## 🎬 Demo Script (Hackathon Presentation)

1. **Open Dashboard** → Show live stats (entities, connections, alerts)
2. **AI Insights** → Ask "Who are the key players?" → Get network analysis
3. **Network Graph** → Show the visual network with communities highlighted
4. **Upload Document** → Upload a sample FIR PDF → Watch entities auto-extract
5. **Entities Page** → Show Rajan Khanna's 97% risk score
6. **Cases Page** → Show "Operation Narco-Web" with linked entities
7. **Alerts Page** → Show detected suspicious patterns

---

## ⚡ Performance

- Graph queries: < 100ms for 1000-node subgraphs (Neo4j)
- Entity extraction: ~2-5s per document (spaCy pipeline)
- Analysis run: < 1s for full centrality computation (NetworkX)
- Frontend: Handles 10,000+ nodes via Cytoscape.js virtual rendering

---

*Built for Smart India Hackathon | NCRB Women Safety Division | Ministry of Home Affairs*
