# CxC — Hackathon Submissions

Two projects from the **CxC Hackathon** (University of Waterloo Data Science Club). Both placed.

| Project | Track | Result | Highlights |
|---|---|---|---|
| [**InvestorIntel**](./InvestorIntel) | RunQL Challenge | 🏆 **Winner** | 6 years of Canadian VC data · Prophet time-series forecasting · interactive Tableau dashboard |
| [**EngageIQ**](./EngageIQ-FederatoEdition) | Federato Challenge | 🥇 **Best Visualization** | 15.4M events ingested into DuckDB · K-means clustering · propensity score matching · Markov chain user journey analysis |

**Team:** Anwesha Bali · Anusheh Atif · Krish Khasnobish · Shreshth Sharma

---

## InvestorIntel

An analysis of Canadian venture capital activity from 2019–2024, plus a 5-year forecast.

We worked with a dataset of investment transactions covering deal size, funding stage, investor activity, industry distribution, and regional trends. The goal was to give startups and policymakers a clear read on the Canadian investment landscape and what it would take to secure funding in it.

**Key findings**
- Deal flow in Canada is **inversely correlated with interest rates** — investment behavior is highly elastic to macro conditions
- Canadian VC is **risk-averse and early-stage-heavy**: pre-seed through Series A dominate, late-stage funding is rare
- **Toronto and BC** lead in activity and show resilience even in high-rate years
- Most-funded sectors: SaaS, AI/ML, Fintech, HealthTech, Data
- Most active investors: BDC Capital, Golden Ventures

**What we built**
- Data cleaning pipeline using Power Query and SQL on RunQL
- Tableau dashboard with heatmaps, stacked bars, and pie charts across investment trends, funding stages, investor demographics, and regional breakdowns
- 5-year forecast using **Facebook Prophet** with logistic growth, capped capacity, and confidence intervals

→ [See the full report and code](./InvestorIntel)

---

## EngageIQ — Federato Edition

A user engagement analysis built on **15.4M real product events** from Federato's RiskOps platform — an underwriting tool used by insurance companies.

The challenge: figure out what drives 28-day retention vs. drop-off, and recommend interventions Federato can ship.

**Scale**
- ~250 CSV files ingested (each 100K–200K rows)
- 15,409,437 total events
- 2,243 unique users · 607,879 unique sessions
- Time range: June 2023 – January 2025

**What we built**
- **Ingestion pipeline**: Python script that cleans, normalizes, and bulk-loads ~250 CSVs into a centralized **DuckDB** database for fast querying
- **Exploratory analysis**: DAU trends, hourly distribution, top event types, top users, 64% 28-day retention rate
- **K-means clustering** on user engagement metrics to segment users into 4 groups (inactive 73%, casual 22%, power users 5%, bots 0.1%) — used the elbow method with KneeLocator to pick *k*
- **Propensity score matching** (logistic regression + nearest-neighbor matching) to causally identify which event types drive engagement
- **Markov chain user journeys** with bottleneck detection — identified transitions with <20% probability as drop-off points
- **Regional and device-type analysis** — found Windows users are 2.5× more likely to have above-average session duration

**Headline insight**: 99.9%+ drop-off rates between certain event categories and the submission workflow signal critical UX bottlenecks the platform should fix.

→ [See the full report and code](./EngageIQ-FederatoEdition)

---

## About CxC

CxC (Computer Science Challenge) is the University of Waterloo Data Science Club's annual hackathon. Industry partners provide real datasets, teams have ~24 hours, and judges from those companies score submissions.

## About Me

Anwesha Bali — Computer Science (Waterloo) + Business Administration (Laurier), Double Degree program.

[LinkedIn](https://linkedin.com/in/anwesha-bali) · [Portfolio](https://flourishing-raindrop-c85f26.netlify.app/) · [Email](mailto:a2bali@uwaterloo.ca)
