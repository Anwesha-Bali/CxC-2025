# InvestorIntel

🏆 **Winner — RunQL Challenge, CxC Hackathon 2025**

A data-driven look at Canadian venture capital from 2019–2024, with a 5-year forecast and recommendations for startups trying to raise money in this market.

**Team:** Anwesha Bali · Anusheh Atif · Krish Khasnobish · Shreshth Sharma

---

## The challenge

RunQL gave us a dataset of Canadian investment transactions across 6 years — deal sizes, funding rounds, investor participation, industry breakdowns, regional trends — and asked us to surface what it actually means for founders and investors operating in this market.

## Headline findings

- **North America leads** in volume and diversity of investments; Canadian capital favors early-stage deals
- **Deal flow is inversely correlated with interest rates** — Canadian investors are highly elastic to macro conditions, making fundraising timing matter a lot
- **Canada's VC scene is risk-averse**: Pre-seed → Series A dominates; Series B+ is rare and frequently has to come from US/foreign capital
- **Toronto and BC** lead in activity and stay relatively resilient through high-rate years; the East Coast and Ottawa receive disproportionately less
- **Hot sectors (consistently 2019–2024):** SaaS, AI/ML, Fintech, HealthTech, Data
- **Most active firms:** BDC Capital, Golden Ventures — both focused on high-growth startups across diverse rounds

## Methodology

### Data cleaning
- Removed duplicates to prevent double-counting deals
- Dropped rows with critical nulls
- Corrected typos
- Cleaning done in **Power Query** (Power BI) and **SQL on RunQL**

### Visualization
- Built an interactive **Tableau dashboard** covering:
  - Investment trends over time
  - Funding stage analysis
  - Investor demographics & behavior
  - Sectoral & regional insights
- Visual mix: heatmaps, pie charts, stacked bar charts

### Forecasting
We used **Facebook Prophet** to forecast total Canadian investment dollars from 2025–2030. Key modeling choices:

- **Logistic growth** (`growth="logistic"`) — investment volume can't grow unbounded, so a saturating curve fit better than linear
- **Custom capacity and floor**: cap set to 1.5× max observed value, floor set to 0.8× min — prevents the forecast from collapsing to zero in low-confidence periods
- 80% confidence intervals visualized as a shaded band

The forecast shows periodic fluctuations but an overall upward trend over the next 5 years.

## Recommendations (the deliverable)

**For startups raising in Canada:**
1. **Raise early.** Late-stage capital is scarce here — go to US/foreign VCs for Series B+ if needed
2. **Acknowledge the risk-aversion.** Lead with risk mitigation, not just upside
3. **Time the macro.** Canadian deal flow correlates strongly with interest rates — raising in low-rate windows is materially easier

**For investors / VCs:**
- Lean into SaaS, Fintech, AI/ML at early stages
- Toronto and Quebec are the highest-activity geographies for sourcing

**For policymakers:**
- East Coast and Ottawa underinvestment is a real gap
- Late-stage funding scarcity (Series C+) is a structural weakness worth addressing through policy

## Repo structure

```
InvestorIntel/
├── [Python forecasting script]
├── [data files]
├── [Tableau workbook / link]
└── CxC_RunQL_Report.pdf    # full written report
```

## What I'd do differently

- The forecasting model uses annual aggregates — moving to quarterly would surface within-year patterns
- A SCD-style historical investor table would let us track investor evolution over time, not just snapshots
- Tableau was great for judging but a Power BI version with row-level security would be more practical for the policymaker audience

## Built at

[CxC Hackathon 2025](https://uwdsc.com/) · University of Waterloo · February 2025
