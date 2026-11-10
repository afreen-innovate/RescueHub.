# 🚨 RescueHub

> **Emergency Response & Disaster Management Platform: one-tap SOS reporting, rule-based incident prioritization, and location-based team dispatch on a live map.**

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![React](https://img.shields.io/badge/frontend-React-61DAFB.svg)
![Tailwind](https://img.shields.io/badge/style-Tailwind%20CSS-38BDF8.svg)
![Leaflet](https://img.shields.io/badge/maps-Leaflet%20%2B%20OpenStreetMap-199900.svg)

**🔗 Live Demo:** `<add deployed link>`
**🎥 Demo Video:** `<add video link>`
**🔑 Admin Demo Access:** open `/admin` and enter the demo password `rescue2026` *(demo only; see the disclaimer below)*

> 🚨 **In immediate danger? Call 112 first.** RescueHub supports emergency coordination and does not replace emergency services.

---

## 📌 Problem Statement

During disasters and emergencies, coordination breaks down in four places:

1. **Slow, complicated reporting:** People in distress cannot fill long forms or explain where they are.
2. **No clear priority:** Responders receive many incidents with no consistent way to decide which one needs help first.
3. **Manual dispatch:** Choosing the nearest suitable team means checking locations, categories and availability by hand.
4. **No visibility for the reporter:** After reporting, people do not know whether help is on the way.

**RescueHub** gives citizens a no-login, one-tap way to report an emergency with live location, ranks every open incident with a transparent priority score, and helps admins assign the nearest available team of the right category. Reporters can track their report with a status timeline and ETA.

---

## 🚀 Features

> Update the **Status** column to *Production / Beta / Planned* after testing the deployed app.

| Feature | Area | Mechanism | Output | Status |
|---|---|---|---|---|
| **SOS Button** | Citizen home | One tap saves a Critical report with live location | Report ID | Verify |
| **Emergency Report Form** | Citizen | One-screen form: category icon cards, auto-filled title, quick chips, people-affected stepper, optional contact | Saved incident | Verify |
| **Live Location Sharing** | Citizen | Keeps updating while the page is open; saved every 5 seconds after submit; map-tap fallback if blocked or slower than 5 seconds; submit is never blocked | Updating coordinates | Verify |
| **Track My Report** | Citizen | Enter report ID (`RH-` plus 6 letters/numbers) | Status timeline, assigned team and ETA | Verify |
| **Severity Rules** | Logic | Category base level, raised by people count; SOS is always Critical | Low / Medium / High / Critical | Verify |
| **Intelligent Prioritization** | Logic | Priority score from severity, people, time waiting and category; recalculated every 3 seconds | Ranked open incidents | Verify |
| **Emergency Dashboard** | Admin | KPI cards, category bar chart, ranked table, filters, LIVE badge, red glow on critical incidents | Command view | Verify |
| **Incident Management** | Admin | View details, change status and severity, delete with confirmation, live map refreshing every 3 seconds | Updated incident | Verify |
| **Auto-Assign Nearest Team** | Admin | Haversine distance to available teams of the suitable category; candidate list for manual choice | Assignment, route line, km, ETA | Verify |
| **Team Management** | Admin | Create, edit and delete teams with category, location, availability | Team roster | Verify |
| **Seed Data** | Demo | 8 teams around Chennai and 6 sample incidents loaded if the database is empty | Ready-to-demo data | Verify |

---

## 🏗️ Tech Stack

> Fill the **Version** column from `package.json`.

| Component | Version | Role | Rationale |
|---|---|---|---|
| **React** | see `package.json` | UI framework | Component-based screens for citizen and admin sides. |
| **Tailwind CSS** | see `package.json` | Styling | White and red citizen UI; dark admin dashboard; responsive layout. |
| **Leaflet** | see `package.json` | Interactive maps | Incident and team pins, route line, tap-to-set location. |
| **OpenStreetMap** | n/a | Map tiles | Free map data, no API key needed. |
| **Built-in database** | n/a | Two tables: `incidents`, `teams` | Simple storage for the demo. |

---

## 📂 Project Structure

> Replace with the real tree from the final build.

```
RescueHub/
├── src/
│   ├── pages/
│   │   ├── Home.jsx            # SOS button, Report Emergency, Track My Report
│   │   ├── Report.jsx          # One-screen emergency report form
│   │   ├── Track.jsx           # Status timeline and assigned team
│   │   └── admin/              # Dashboard, Incident details, Team management
│   ├── lib/
│   │   ├── severity.js         # Severity rules
│   │   ├── priority.js         # Priority score and level
│   │   ├── dispatch.js         # Suitable team, Haversine distance, ETA
│   │   └── reportId.js         # RH-XXXXXX generation with retry on duplicates
│   └── components/             # Map, badges, cards, charts
├── .env.example
└── README.md
```

---

## ⚙️ Installation & Setup

### Prerequisites
- Node.js (LTS) and npm
- Git

### Step 1: Clone the Repository
```bash
git clone https://github.com/afreen-innovate/RescueHub.git
cd RescueHub
```

### Step 2: Environment Variables
```bash
# Linux / macOS
cp .env.example .env

# Windows (PowerShell)
Copy-Item .env.example .env
```
Fill in any values listed in `.env.example`. Never commit `.env`.

### Step 3: Run the App
```bash
npm install
npm run dev
```

### Step 4: Verify
1. On the home page, tap **Report Emergency**, submit a report and note the Report ID.
2. Open **Track My Report** and enter the ID.
3. Open `/admin`, enter the demo password, and confirm the report appears in the ranked table.
4. Open the incident and use **Auto-assign nearest available team**. Check that the team becomes Busy and the incident becomes Assigned.

---

## 🧠 How It Works

```
Citizen ──► SOS / Report form ──► incidents table ──► Priority ranking (every 3 s)
                                          │                       │
                                          ▼                       ▼
                                  Track My Report         Admin dashboard + map
                                                                  │
                                              Auto-assign (Haversine) ──► teams table
```

### 1. Report ID
`RH-` followed by 6 random letters/numbers. If the ID already exists, a new one is generated and the save is retried.

### 2. Severity
- Base: Fire, Building Collapse, Medical = High; Flood, Road Accident = Medium; Other = Low.
- +1 level if people ≥ 6, and +1 more if people ≥ 20, up to Critical.
- SOS is always Critical.

### 3. Priority Score
`score = severity points (Low 10, Medium 25, High 40, Critical 55) + min(people, 20) × 1.5 + min(minutes since reported, 60) × 0.25 + category points`

Category points: Fire/Building 10, Medical 9, Flood 7, Road 6, Other 3.
Priority level: ≥ 80 Critical, ≥ 55 High, ≥ 35 Medium, otherwise Low.

### 4. Suitable Team and Dispatch
- Fire → Fire team; Medical and Road Accident → Medical team; Flood, Building Collapse and Other → Rescue team.
- Auto-assign picks the nearest available team of the suitable category using Haversine distance. ETA = distance ÷ 40 km/h.
- Assigning sets the team Busy and the incident Assigned; resolving sets the team Available.

---

## 📈 Scalability

This is a demo-scale build. Planned improvements for real traffic:

- Move to a managed backend with authentication and row-level security.
- Push single-row realtime updates instead of re-polling every few seconds.
- Paginate and virtualize long incident tables; load only open incidents by default.
- Add rate limits and idempotency keys to prevent duplicate reports during spikes.

---

## 💡 Feasibility

- **Free tooling:** React, Tailwind, Leaflet and OpenStreetMap need no paid map API.
- **Simple data model:** Two tables (`incidents`, `teams`) keep the system easy to understand and extend.
- **Fast to demo:** Seed data loads automatically, so the full flow works on first launch.

---

## 🌟 Novelty & Comparative Analysis

| Capability | RescueHub | Phone / messaging-group reporting | Generic form tools |
|---|---|---|---|
| One-tap SOS with live location | ✅ | ⚠️ Manual | ❌ |
| Transparent priority ranking | ✅ Formula-based | ❌ Ad hoc | ❌ |
| Nearest-team suggestion | ✅ Haversine + category match | ❌ Manual | ❌ |
| Reporter tracking | ✅ Timeline and ETA | ❌ | ⚠️ Rare |
| No login for citizens | ✅ | ✅ | ⚠️ |

### Core Innovation
Prioritization and dispatch use simple, visible formulas, so staff can see why an incident ranks where it does and which team is suggested and why.

---

## 🔧 Feature Depth & Edge-Case Engineering

### 1. Edge Cases
- **Location blocked or slow:** After 5 seconds, or if blocked, the user taps the map to set the location. Submit is never blocked.
- **Duplicate report ID:** A new ID is generated and the save is retried.
- **No suitable team available:** The admin sees the candidate list and can choose manually.
- **Test or duplicate reports:** Admins can delete them after a confirmation.
- **Stale live location:** The LIVE badge shows only if the location updated in the last 30 seconds.

### 2. Example Incident Record
Illustrative; adjust to the actual table.

```json
{
  "id": "RH-A7K29Q",
  "title": "Fire Emergency",
  "category": "Fire Emergency",
  "description": "Fire spreading",
  "people_affected": 8,
  "severity": "Critical",
  "status": "Reported",
  "lat": 13.0827,
  "lng": 80.2707,
  "location_text": "Near main road",
  "contact": null,
  "assigned_team_id": null,
  "created_at": "2026-11-10T10:30:00Z",
  "location_updated_at": "2026-11-10T10:30:05Z"
}
```

---

## ⚠️ Ethical Use & Operational Disclaimer

RescueHub is a coordination aid and a demo.

- **Call 112 first.** It does not replace emergency services.
- **Human decision-making:** Priority scores and team suggestions are aids. Admins decide status, severity and assignments.
- **Demo security:** The demo lets anyone read and write data without login, and the admin password is simple and visible. Do not use it with real emergencies or personal data. Add authentication, access rules and data-retention controls before real use.
- **Privacy:** Reports contain locations and optional contact details. Handle them responsibly.

---

## 📜 License

Licensed under the **MIT License**. See the [LICENSE](LICENSE) file.

---

## 🧩 Author & Team

**Afreen**
- **GitHub:** [afreen-innovate](https://github.com/afreen-innovate)
- **Email:** `<add email>`
- **Project Repository:** [https://github.com/afreen-innovate/RescueHub](https://github.com/afreen-innovate/RescueHub)
