# Quantitative Data Analyst Intern — Take-Home Assessment

## Objective
Analyze how BSE corporate announcements affect stock price and trading volume for five companies (RELIANCE, HDFCBANK, NYKAA, HAL, RVNL) over Aug 2023 – Aug 2026.

## Methodology

### 1. Data Preparation & Validation
- Parsed BSE corporate announcements from `corporate_announcements.csv` (2,530 records).
- Used `DissemDT` as primary timestamp, `DT_TM` as fallback.
- Loaded 1-minute OHLCV data for each stock (~277K bars each).
- Data quality checks: duplicates, missing values, invalid OHLC, zero-volume bars, time gaps.
- Aligned each announcement to the first complete 1-min trading bar at or after dissemination.
- Pre/post-market and weekend announcements map to next session's 09:15 bar.

### 2. Subject Taxonomy & Event Clustering
- Rule-based classification into 13 subject groups using `CATEGORYNAME`, `SUBCATNAME`, `NEWSSUB`.
- Event clustering: same stock + same subject within ±2 hours = one independent event.
- 2,530 raw announcements → ~1,800+ independent events.

### 3. Price & Volume Impact
- Windows: 5-min, 30-min, 60-min, session-close, 1/5/10/20 trading sessions.
- Metrics: returns, volume ratio vs 20-bar baseline, high-low range volatility.
- Statistics: mean, median, n, 95% CI, t-test, Kruskal-Wallis across subjects.

### 4. PEAD Analysis
- D0 = first tradable session for earnings info.
- D0 return sign = market-implied direction.
- Drift measured at D+1, D+3, D+5, D+10, D+20.
- Pooled + stock-wise breakdown with CIs and t-tests.

## Setup & Reproduction

```bash
pip install -r requirements.txt
jupyter notebook Quant_Analyst_Assessment.ipynb
```

Place the data files in the same directory:
- `corporate_announcements.csv`
- `RELIANCE.csv`, `HDFCBANK.csv`, `NYKAA.csv`, `HAL.csv`, `RVNL.csv`
- `metadata.json`

Run all cells top-to-bottom. Outputs are written to `outputs/`.

## Main Findings
1. Announcements cause a measurable volume surge (avg ~2x baseline in 30 min).
2. Financial Results drive the largest price-volume response.
3. ~74% of announcements are post-market, making overnight gaps critical.
4. Routine filings show negligible price impact.
5. Post-earnings drift is weak and inconsistent — limited evidence of under-reaction.
6. Cross-stock heterogeneity in return volatility reflects liquidity differences.
7. Event clustering reduces raw count by ~30%, preventing double-counting.
8. Volume ratio discriminates announcement types better than returns.

## Limitations
- Only 5 stocks over 3 years; not generalizable to the broader market.
- No analyst-expectation or earnings-surprise data; PEAD is price-conditioned only.
- 2-hour clustering window is heuristic.
- Overnight gap conflates announcement-specific and general market movement.
- Volume baseline uses 20 pre-event bars; may not capture time-of-day patterns.

## AI Tool Usage
This analysis was developed with AI-assisted coding tools ChatGpt. All code, assumptions, and interpretations are the author's responsibility.
