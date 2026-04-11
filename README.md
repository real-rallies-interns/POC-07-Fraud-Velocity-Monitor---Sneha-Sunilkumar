# Fraud Velocity Monitor
### Real Rails Intelligence Library · PoC #07 · Payment Rail

![Archetype](https://img.shields.io/badge/Archetype-Temporal-818CF8?style=flat-square&labelColor=030712)
![Rail](https://img.shields.io/badge/Rail-Payment-38BDF8?style=flat-square&labelColor=030712)
![VAR](https://img.shields.io/badge/VAR-18%2F18%20PASS-065F46?style=flat-square&labelColor=D1FAE5)
![Stack](https://img.shields.io/badge/Stack-Next.js%2016%20%2B%20FastAPI-0B1117?style=flat-square)

> Real-time payment fraud intelligence dashboard. Detects velocity anomalies, classifies three spike types (Velocity Burst / Risk Surge / Amount Spike), and surfaces IP/device fingerprint patterns across a 24-hour synthetic transaction stream sourced from CFPB and FRED schema.

---

## What It Does

The dashboard ingests a minute-resolution synthetic transaction stream, applies velocity rules, and flags anomalies in real time. Click any triangle spike on the signal stream to open the **Anomaly Inspector** showing time, fraud count, risk score, peak velocity, device fingerprint, and IP hash.

| Spike Type | Color | Trigger | Meaning |
|---|---|---|---|
| Velocity Burst | `#F87171` Red | Multi-fraud txns per bucket | Card testing / Account Takeover |
| Risk Surge | `#FBBF24` Amber | Avg risk > 0.55 | Device/IP clustering |
| Amount Spike | `#818CF8` Indigo | Abnormal $ volume | Wire fraud / Cash-out |

---

## Stack

### Frontend
| Package | Version | Purpose |
|---|---|---|
| Next.js | 16.2.2 | App Router framework |
| React | 19.2.4 | UI rendering |
| TypeScript | ^5 | Type safety |
| Tailwind CSS | ^4 | Utility styling |
| Recharts | ^3.8.1 | Signal stream charts |

### Backend
| Package | Purpose |
|---|---|
| FastAPI 2.0.0 | REST API server |
| Pandas | Signal stream aggregation |
| NumPy | Synthetic data generation |
| Uvicorn | ASGI server |

### Data Sources
| Source | Usage |
|---|---|
| CFPB | Payment complaint schema — synthetic event stream labels |
| FRED (DRCCLACBS) | Consumer delinquency rate — macro anomaly baseline (mock) |
| Synthetic stream | 1,440-minute event stream with fraud / suspicious / normal labels |

---

## Project Structure

```
fraud-velocity-monitor/
├── backend/
│   └── main.py                          # FastAPI — all API endpoints + data generation
├── frontend/
│   ├── app/
│   │   ├── globals.css                  # Real Rails DNA variables + Google Fonts
│   │   ├── layout.tsx                   # Root layout — metadata + body classes
│   │   └── page.tsx                     # Main dashboard — 70/30 Temporal stage
│   ├── package.json
│   └── tsconfig.json
├── VAR_PoC07_Fraud_Velocity_Monitor.pdf # Visualization Audit Report (Full Green)
├── UAT_PoC07_Fraud_Velocity_Monitor.pdf # Functional UAT checklist
└── README.md
```

---

## How to Run

### Prerequisites
- Node.js 18+
- Python 3.10+

### 1. Start the Backend

```bash
cd backend
pip install fastapi uvicorn pandas numpy
uvicorn main:app --reload --port 8000
```

- API: `http://localhost:8000`
- Docs: `http://localhost:8000/docs`

### 2. Start the Frontend

```bash
cd frontend
npm install
npm run dev
```

- Dashboard: `http://localhost:3000`

> **Offline mode:** If the backend is not running, the dashboard automatically activates client-side synthetic data — all tabs, charts, and the inspector panel work without the backend.

---

## API Reference

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/health` | Health check + version |
| GET | `/api/signal-stream?resolution=5min&hours_back=24` | Core temporal stream |
| GET | `/api/anomalies` | All classified anomaly events |
| GET | `/api/metrics/summary` | KPI card data |
| GET | `/api/metrics/velocity-heatmap` | Hour-of-day fraud heatmap |
| GET | `/api/metrics/category-breakdown` | Fraud rate by merchant category |
| GET | `/api/review-queue` | Flagged events sorted by risk score |
| GET | `/api/download/sample-data` | 100-row signal stream CSV |

**Resolution options:** `1min` · `5min` · `15min` · `1h`

---

## Dashboard Features

### 70% Main Stage — Signal Stream (Temporal Archetype)
Three stacked signal tracks:
1. **Transaction Signal Stream** — Stacked area (Normal / Suspicious / Fraud) with clickable ▲ anomaly markers
2. **Velocity Track** — Color-coded bars with 15/hr rule threshold line
3. **Volume Track** — USD transaction volume area chart

**Anomaly Inspector** — click any triangle to see:
- Time · Fraud Txns · Avg Risk · Peak Velocity · Device Fingerprint · IP Hash
- Plain-English description of what the anomaly means

### 30% Intelligence Sidebar
| Section | Content |
|---|---|
| **A** | Title + 4 live KPIs (Exposure USD, Avg Fraud Velocity, Anomaly Mins, % Above Avg) |
| **B** | Why This Matters + CFPB/FRED context + FRED delinquency rate (2.61%) |
| **C** | Who Controls the Rail — Card Networks, Regulators, Fraud Engines |
| **D** | Time resolution controls (5min / 15min / 1h) + Refresh |
| **E** | Download signal stream as CSV |

---

## Real Rails DNA Compliance

| Rule | Status |
|---|---|
| Background `#030712` Obsidian Black | ✅ Mandatory — triple confirmed in CSS + TSX |
| 70% Main Stage / 30% Intelligence Sidebar | ✅ `gridTemplateColumns: "70% 30%"` |
| Archetype: Temporal — click spikes on timeline | ✅ Full inspector with scrollIntoView |
| Sections A–E in sidebar | ✅ All present |
| Cyan `#38BDF8` for active states | ✅ Tabs, KPIs, header |
| IBM Plex Mono + Space Grotesk | ✅ Google Fonts with display=swap |
| Mock fallback when API offline | ✅ All 6 API calls wrapped in fetchFallback() |
| No hardcoded credentials | ✅ `.env` only |
| ARCHETYPE badge in header | ✅ Indigo pill — "ARCHETYPE: TEMPORAL" |

---

## Audit Status

| Document | Result |
|---|---|
| Visualization Audit Report (VAR) | **Full Green — 18/18 PASS** |
| Bug fixes applied | **12 issues resolved across 3 iterations** |
| Functional UAT | **All test cases PASS** |

---

## Key Bug Fixes (Summary)

| # | Issue | Fix |
|---|---|---|
| 1 | Charts showed `width(-1) height(-1)` — blank screen | `minHeight:0` on grid, main, and tab content div |
| 2 | Inspector panel invisible after click | `minHeight:"100%"` → `minHeight:0` on tab content |
| 3 | Click opened inspector on non-spike points | Added `if (!raw?.anomaly) return` guard |
| 4 | Tooltip clipping at chart edges | `.recharts-wrapper { overflow: visible !important }` |
| 5 | Review Queue / Category tabs empty | `genCategoryBreakdown()` / `genReviewQueue()` as real fallback |
| 6 | Font appearing compressed/small | `letter-spacing:normal`, `font-size:14px` body, `text-sm` class |

---

## Author

Real Rails Intelligence Library — PoC #07
Intern Pilot Program · Payment Rail Track
