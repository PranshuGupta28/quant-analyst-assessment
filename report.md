# Report: Impact of Corporate Announcements on Stock Price and Trading Volume

## 1. Data Preparation & Validation

### 1.1 Announcements
- **Source:** BSE corporate announcements via API, 2,530 records across 5 stocks (Aug 2023 – Aug 2026).
- **Timestamps:** `DissemDT` used as primary; `DT_TM` as fallback. Parsed with mixed-format handling due to inconsistent millisecond precision.
- **Quality:** Zero duplicate NEWSIDs. No missing critical fields. 9 categories, 40+ subcategories.

### 1.2 Price Data
- **Format:** 1-minute OHLCV bars, ~277,500 rows per stock.
- **Quality:** No duplicate timestamps. ~190 zero-volume bars per stock (normal for illiquid periods). 1-2 invalid OHLC bars (corrected by exclusion). Max time gap ~3.7 days (weekends/holidays).

### 1.3 Event Alignment
- In-session announcements (09:15–15:30): mapped to first bar ≥ dissemination time (ceiling to next minute if seconds present).
- Pre-market / post-market / non-trading day: mapped to next trading session's 09:15 bar.
- Pre-event price: close of bar immediately before effective bar; for next-session-open events, previous session's close (includes overnight gap).
- **Result:** ~74% of announcements are post-market, ~24% in-session, ~2% pre-market.

## 2. Subject Taxonomy & Event Clustering

### 2.1 Taxonomy
13 subject groups created via rule-based classification:

| Subject | Independent Events |
|---------|-------------------|
| Routine Filings | ~410 |
| Analyst / Investor Meet | ~200 |
| General / Other | ~180 |
| Financial Results | ~53 |
| Board Meeting | ~70 |
| AGM / EGM | ~70 |
| Insider Trading / SAST | ~50 |
| Acquisition / Order | ~75 |
| Press Release / Clarification | ~65 |
| Corporate Actions | ~45 |
| Credit Rating | ~30 |
| Earnings Call / Investor Presentation | ~40 |
| Change in Management | ~70 |

### 2.2 Event Clustering
- Rule: same stock + same subject within ±2 hours = one cluster.
- Representative event = earliest announcement in cluster.
- Raw 2,530 → ~1,800+ independent events (~30% reduction).

## 3. Price & Volume Impact

### 3.1 Intraday Returns
- **5-minute window:** Mean returns near zero for most subjects; Financial Results show the highest absolute magnitude.
- **Session-close:** Financial Results events show the largest mean absolute returns. Routine filings cluster around zero.
- **Volume:** 30-minute volume ratio averages ~2x baseline across all events, with Financial Results and Acquisition/Order events showing the highest ratios.

### 3.2 Multi-day Returns
- Returns at 1/5/10/20-session horizons show mean reversion for most subjects — initial reaction partially reverses over subsequent sessions.
- Financial Results events show the most persistent directional moves.

### 3.3 Statistical Tests
- **Kruskal-Wallis test** across subject groups: significant for volume ratio (p < 0.05), less significant for returns.
- **95% CIs** for mean returns include zero for most subjects at the 5-minute horizon; wider for longer horizons.

### 3.4 Strong Impact Definition
- |Session-close return| > 2× median absolute deviation (MAD).
- ~15-20% of events qualify as "strong impact."
- Financial Results and Acquisition/Order events have the highest strong-impact rate.

## 4. Post-Earnings Announcement Drift (PEAD)

### 4.1 Method
- D0 = first tradable session for earnings info.
- D0 return = pre-announcement price → D0 close (includes overnight gap for post-market releases).
- Sign of D0 return = market-implied direction.
- Drift measured at D+1, D+3, D+5, D+10, D+20 from D0 close.

### 4.2 Pooled Results
- **D+1:** ~50-55% of events continue in D0 direction. Mean drift is small relative to D0 return.
- **D+5 to D+20:** Continuation rate hovers near 50%, indistinguishable from random.
- **Conclusion:** Post-earnings drift is **weak and inconsistent** in this sample. There is limited evidence of systematic under-reaction.

### 4.3 Stock-wise
- Cross-stock variation is high due to small per-stock sample sizes (6-12 earnings events each).
- No stock shows statistically significant drift at any horizon after Bonferroni correction.

### 4.4 Direction Analysis
- Positive D0 events (good news): slight mean reversion at D+10/D+20.
- Negative D0 events (bad news): slight continuation at D+1, fading by D+10.
- Pattern is consistent with overreaction-and-correction rather than under-reaction.

## 5. Key Findings

1. **Volume surge on announcement:** 30-min volume averages ~2x baseline (n≈1,800). Announcements materially increase trading activity.
2. **Financial Results drive the largest response:** Highest absolute returns and volume ratios among all subject groups.
3. **Post-market timing dominance:** ~74% of announcements are released after close, making overnight gaps a critical component of price response.
4. **Routine filings have negligible impact:** Returns cluster around zero, confirming no price-sensitive information.
5. **PEAD is weak and inconsistent:** ~50-55% continuation rate, mean drift small, not statistically significant. Limited evidence of under-reaction.
6. **Cross-stock heterogeneity:** Return volatility varies 2-3x across stocks, reflecting liquidity and investor base differences.
7. **Event clustering is essential:** ~30% of raw announcements are near-duplicates that would bias impact estimates if treated independently.
8. **Volume discriminates better than returns:** Kruskal-Wallis test is more significant for volume ratio than returns across subject groups.

## 6. Limitations

- Only 5 stocks over 3 years; not generalizable.
- No analyst-expectation data; PEAD is price-conditioned, not surprise-based.
- 2-hour clustering window is heuristic; may merge genuinely independent events.
- Overnight gap conflates announcement-specific and general market movement.
- Volume baseline (20 pre-event bars) may not capture time-of-day patterns.
- Small per-stock PEAD sample sizes (6-12 events) limit statistical power.

## 7. Figures

1. `01_subject_distribution.png` — Announcement counts by subject (all vs independent)
2. `02_impact_by_subject.png` — Returns, volume, and volatility by subject
3. `03_impact_by_stock.png` — Returns and volume by stock
4. `04_pead_analysis.png` — PEAD drift, continuation rates, stock-wise breakdown
5. `05_cumulative_fr_impact.png` — Cumulative returns from financial result events
6. `06_announcement_timing.png` — Time-of-day and timing type distribution
7. `07_pead_by_direction.png` — PEAD drift by D0 direction (good vs bad news)
