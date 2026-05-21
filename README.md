# 📦 Alok Bags — Business Ledger & Manufacturing ERP

A full-featured, dark-mode ERP system for **Alok Bags** — a bags manufacturing business. Built with **React 19 + Vite** (frontend) and **PHP + MySQL** (backend API), featuring live analytics, AI-powered demand forecasting, and a fully responsive collapsible sidebar layout.

---

## 🚀 Features

| Module | Description |
|---|---|
| **Dashboard** | Live KPI tiles, dynamic SVG charts (Area, Bar, Donut), AI forecasting |
| **Sales** | Tax invoices, payment tracking, GST breakdowns |
| **Purchases** | Vendor bills with OCR & AI parsing |
| **Payments** | Transaction ledger with in/out tracking |
| **Ledger** | Party-wise accounts and running balance |
| **Inventory** | Product stock management with edit support |
| **Production** | Job order tracking and yield management |
| **Dispatch** | Material dispatch logs |
| **GST** | Tax filing summaries |
| **Reports** | P&L and Balance Sheet views |
| **AI Analyst** | Conversational Gemini-powered business advisor |

---

## 🔧 Setup

### Prerequisites
- **XAMPP** (PHP 8.x + MySQL) running on `localhost`
- **Node.js** 18+ and npm

### Backend Setup
1. Start Apache and MySQL in XAMPP.
2. Place the project folder at: `C:/xampp/htdocs/alok-bags/`
3. Run the migrations at: `http://localhost/alok-bags/api/run_all_migrations.php`
4. This creates all tables including `users`, `products`, `invoices`, `transactions`, etc.

### Frontend Setup
```powershell
cd alok-bags-react
npm install
npm run dev
```

Open `http://localhost:5173` in your browser.

---

## 📐 Architecture

```
alok-bags/
├── api/                  ← PHP REST API endpoints
│   ├── config.php        ← DB connection + Auth helpers
│   ├── auth.php          ← Login / signup (optional)
│   ├── dashboard_stats.php ← Live KPI & chart data
│   ├── ai_forecaster.php ← Statistical ML demand forecasting
│   ├── invoices.php
│   ├── parties.php
│   ├── products.php
│   ├── payments.php
│   └── ...
├── alok-bags-react/      ← React + Vite frontend
│   └── src/
│       ├── components/
│       │   ├── Layout.jsx   ← Collapsible sidebar layout
│       │   └── AIChatbot.jsx
│       ├── pages/
│       │   ├── Dashboard.jsx ← Live charts + AI panel
│       │   ├── Sales.jsx
│       │   ├── Inventory.jsx
│       │   ├── Payments.jsx
│       │   └── ...
│       └── utils/api.js
└── db.sql                ← Initial database schema
```

---

## 📊 AI Forecasting — Algorithm Documentation

The demand forecasting system (`api/ai_forecaster.php`) combines **local statistical ML algorithms** with optional **Gemini AI enrichment** when an API key is provided.

### 1. 📈 Simple Linear Regression (Sales Trend)

Projects the next month's expected billing total using historical invoice data.

**Formula:**
```
y = mx + c

Where:
  x = Month index (1, 2, 3, ... N)
  y = Monthly billing total (INR)
  m = Slope coefficient (growth or decline rate per month)
  c = Y-intercept (baseline billing)

Slope:     m = (N·Σxy - Σx·Σy) / (N·Σx² - (Σx)²)
Intercept: c = (Σy - m·Σx) / N
Predicted: ŷ_{N+1} = m·(N+1) + c
```

**Growth Coefficient:**
```
Growth % = (m / c) × 100
```

**Example output:**
> "Linear regression reveals a +8.3% monthly growth trend. Predicted next-month billing: ₹1,24,500."

---

### 2. 📦 Sales Velocity (Average Daily Demand)

Calculates how fast each product is being sold based on the last 60 days of invoice data.

**Formula:**
```
Velocity (v) = Total Quantity Sold (last 60 days) / 60

Units: [product unit] per day
```

---

### 3. 🔁 Reorder Point (ROP)

Triggers a restock alert when current stock falls below this mathematically derived threshold.

**Formula:**
```
ROP = (v × Lead Time) + Safety Stock

Where:
  v            = Daily sales velocity
  Lead Time    = 10 days (standard procurement lead time)
  Safety Stock = Monthly Average Demand × 20% buffer

Safety Stock = (v × 30) × 0.20
```

**Minimum floor:** If ROP < 150 units, a floor of 150 is applied to prevent false "optimal" readings on new or slow-moving SKUs.

**Alert Condition:**
```
Restock Needed = (Current Stock ≤ ROP)
```

---

### 4. 🤖 Gemini AI Enrichment (Optional)

When a valid Gemini API key is configured in `api/config.php`, the programmatic statistical output (regression results, velocity, ROP) is injected as structured context into a Gemini 2.5 Flash prompt. The AI generates a natural-language executive summary and enriched item-level reasoning. If the API key is absent or fails, the **local statistical model serves as the primary output** with no degradation in business logic.

---

## 🗺️ Responsive Layout

| Screen | Behavior |
|---|---|
| **Desktop (>768px)** | Persistent vertical sidebar (240px wide), collapse button shrinks to 68px icon-only mode with hover tooltips |
| **Tablet (768px–1024px)** | Charts stack vertically; sidebar collapses to icon mode automatically |
| **Mobile (<768px)** | Sidebar hidden; hamburger icon in top-bar opens an overlay slide-in drawer |

---

## 📝 License

Internal business software for Alok Bags. All rights reserved.
