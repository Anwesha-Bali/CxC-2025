# EngageIQ — Federato Edition

🥇 **Best Visualization Overall — Federato Challenge, CxC Hackathon 2025**

A user engagement and retention analysis built on **15.4 million real product events** from Federato's RiskOps platform — an underwriting tool used by insurance companies.

**Team:** Anwesha Bali · Anusheh Atif · Krish Khasnobish · Shreshth Sharma

---

## The challenge

Federato gave us their event log: ~250 CSV files, 100K–200K rows each, covering 19 months of real user activity from June 2023 to January 2025. The product question: which behaviors drive 28-day retention vs. drop-off, and what should the product team actually do about it?

## Scale

| | |
|---|---|
| Total events | 15,409,437 |
| Unique users | 2,243 |
| Unique sessions | 607,879 |
| Time range | June 2023 – January 2025 |
| Source files | ~250 CSVs |

## Pipeline

```
~250 raw CSVs → cleaning + column normalization → DuckDB (centralized events table)
                                                       ↓
              ┌────────────────────────────────────────┼────────────────────────────────────┐
              ↓                                        ↓                                    ↓
    Exploratory analysis           Segmentation (K-means)              Causal inference (PSM)
    (DAU, retention, top users)    + cluster profiling                 + Markov chain journeys
                                                                                ↓
                                                                       Bottleneck detection
                                                                       Next-best-action recommendations
```

### Ingestion (DuckDB)
We chose **DuckDB** as the warehouse. JSON-derived CSVs were cleaned (column names sanitized for SQL), then bulk-loaded into a unified `events` table using `COPY` commands. This let us run analytical queries over 15M+ rows in seconds without any cloud setup.

## What we did

### 1. Exploratory analysis
- **Daily active users** over time — surfaced two notable engagement dips (2023-11, 2024-06) worth investigating
- **Hourly distribution** — peak activity 12pm–8pm, max at 3pm
- **Top event types** — `account-lines::widget:render` dominated at 2.7M events
- **Power user identification** — single user generating 343K events; 8 of top 10 most active sessions came from one user (likely automation/bot)
- **28-day retention**: 1,434 / 2,243 users = **~64%** returned within 28 days
- **Event categorization** — built a semantic model collapsing raw event types into 11 categories (Render, View, Navigation, Click, Submission, Account Action, Edit/Update, Filter/Config, Document, Submission Workflow, Other) to enable higher-level analysis

### 2. Segmentation: K-means clustering
Clustered users on engagement metrics: total events, total sessions, average session duration, session ends, and unique events.

- Standardized features with `StandardScaler`
- Used the **Elbow Method** with `KneeLocator` to programmatically pick optimal *k*
- Optimal *k* = 4

**Resulting segments:**

| Cluster | Type | Size | Avg Session Dur | Total Events (mean) |
|---|---|---|---|---|
| 1 | Inactive / passive viewers | 1,638 (73.1%) | ~6 min | 171 |
| 0 | Casual users | 499 (22.3%) | ~43 min | 10,107 |
| 3 | Power users | 103 (4.6%) | ~92 min | 91,451 |
| 2 | Outliers / bots | 2 (0.1%) | <1 sec | 108,134 |

The 73% inactive cluster is the biggest retention opportunity — that's where intervention pays off.

### 3. Causal inference: Propensity Score Matching
Used **PSM** to move beyond correlation and ask: *which event categories causally drive engagement?*

- Classified sessions as engaged / not-engaged based on whether duration exceeded the mean (12.45 min)
- Trained logistic regression to estimate propensity scores from event category features
- Used **Nearest Neighbor matching** to balance engaged vs. non-engaged samples
- Computed differences in propensity scores per category

**Findings:**
- ➕ **Render**, **Click**, and **Submission** events drive engagement (positive propensity)
- ➖ **View** and **Navigation** events have *negative* effects — passive browsing without action correlates with disengagement
- **Implication**: The platform should design for action, not just consumption

### 4. User journey analysis: Markov chains
Built a Markov transition matrix over the categorized event sequences, then visualized it as a directed graph.

- Nodes = event categories; edges = transition probabilities
- **Bottleneck detection**: any transition with probability <20% flagged as a potential drop-off point and highlighted in red
- Generated top-15 bottleneck transitions table to make findings actionable

**Top user paths discovered:**
1. `render` (19,157)
2. `view` (17,804)
3. `navigation` (6,793)
4. `click` (4,354)
5. `render → render` (2,987)

**Critical bottlenecks identified:**
- `render → submission_workflow`: 99.998% drop-off
- `view → submission_workflow`: 99.997% drop-off
- `render → account_action`: 99.98% drop-off
- `view → document`: 99.80% drop-off

These are the moments where Federato is losing users — and where targeted UX fixes would have the highest impact.

### 5. Next-best-action recommendations
Used the Markov matrix to compute the top 3 most likely next actions for each event category. This both diagnoses current flows and provides a basis for in-app recommendations to nudge users toward high-engagement paths.

### 6. Bonus: regional and device analysis
- **Regional drop-off heatmap** across 30 regions (filtered to those with 100+ users)
- **Windows vs. non-Windows**: Windows users were **2.5× more likely** to have above-average session duration — suggests real cross-platform UX gaps to address

## Recommendations to Federato

1. **Onboarding intervention for the 73% inactive cluster.** Notification system on first login walking through high-value features.
2. **Fix the submission workflow.** 99.99% drop-off from render/view/click into the submission workflow is the single biggest leak in the funnel.
3. **Design for action, not consumption.** PSM showed view-heavy sessions disengage; add CTAs and "next step" prompts after passive views.
4. **Cross-platform UX audit.** 2.5× engagement gap between Windows and non-Windows is too large to ignore.
5. **Power-user retention program.** 103 power users average 92-min sessions — invite them to beta features, give them advanced analytics.
6. **Investigate the bot cluster.** Two users with the highest event counts and ~0 session time. Either system accounts or automation that should be flagged out of analytics.

## Tech stack

- **Python** (pandas, NumPy, scikit-learn, scipy, networkx, matplotlib, seaborn)
- **DuckDB** for the warehouse layer
- **SQL** for everything that scaled — DAU queries, session duration windows, drop-off analysis, retention calculation
- **K-Means + KneeLocator** for segmentation
- **Logistic Regression + Nearest Neighbor matching** for PSM
- **Markov chain + directed graph** (networkx) for journey analysis
- **Power BI / Tableau** for stakeholder-facing dashboards (won "Best Visualization")

## Repo structure

```
EngageIQ-FederatoEdition/
├── notebook.ipynb           # main analysis (Appendices A–X)
├── ingestion script         # CSV → DuckDB pipeline
├── Federato_Report_and_Visuals.pdf   # full written report
└── visualizations/          # exported charts
```

## What I'd do differently

- The PSM uses logistic regression with relatively simple features — adding behavioral features (time since last event, session position, etc.) would tighten the matched comparisons
- Markov chains assume the next state depends only on the current one. A higher-order or LSTM-based sequence model would catch longer-range dependencies
- The bot detection was a side observation — productionizing it as an automated filter on the analytics pipeline would be a real follow-up project

## Disclaimer

LLMs were used for code commenting and some qualitative descriptions. No code logic was written by an LLM.

## Built at

[CxC Hackathon 2025](https://uwdsc.com/) · University of Waterloo · February 2025
