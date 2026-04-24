# Technical Specification · Accounting & Performance Ratios Model
### ExxonMobil Corporation (XOM) — FY2024

| Created by | Course | Version | LLM Used |
|---|---|---|---|
| Jesse Matthews | BUS-314 | 1.0 · April 2026 | Claude (Anthropic) |

---

## 1. Problem Statement

ExxonMobil Corporation (XOM) is a publicly traded integrated oil and gas company (NYSE: XOM). This specification documents the analytical framework for computing 27 accounting and performance ratios from the company's FY2024 10-K (fiscal year ended December 31, 2024). The objective is to assess financial health, operational efficiency, capital structure, and value creation for a CFO-level briefing prepared as part of BUS-314 Stages 4 and 5.

---

## 2. Inputs (Known Variables)

All figures in millions of USD. Named ranges follow course workbook conventions. FY2023 values serve as start-of-year denominators for turnover and return ratios.

### Balance Sheet — Key Items (FY2024 / FY2023 where both years shown)

| Variable | Named Range | Notes | Value ($M) |
|---|---|---|---|
| Cash & equivalents | `BAL_cash_[year]` | Liquid assets | 23,029 / 31,539 |
| Receivables – net | `BAL_receivables_[year]` | Trade receivables | 43,681 / 38,015 |
| Inventories | `BAL_inventories_[year]` | Finished goods & feedstocks | 19,567 / 25,120 |
| Total current assets | `BAL_assets_current_[year]` | | 90,990 / 96,609 |
| Net PP&E | `BAL_fixed_assets_net_[year]` | Gross PP&E less accum. depr. | 313,554 / 214,940 |
| Total assets | `BAL_assets_total_[year]` | | 453,475 / 376,317 |
| Total current liabilities | `BAL_liabilities_current_2024` | | 72,330 |
| Long-term debt | `BAL_debt_long_term_[year]` | | 35,365 / 37,483 |
| Total liabilities | `BAL_liabilities_total_2024` | | 183,502 |
| Shareholders' equity | `BAL_equity_shareholders_[year]` | | 277,087 / 212,538 |

### Income Statement (FY2024)

| Variable | Named Range | Notes | Value ($M) |
|---|---|---|---|
| Net sales / revenues | `INC_sales` | | 339,247 |
| Cost of goods sold | `INC_cost_goods_sold` | Purchases + manufacturing | 239,063 |
| SG&A | `INC_sga` | | 9,976 |
| Depreciation & depletion | `INC_depreciation` | From income statement | 23,442 |
| EBIT | `INC_ebit` | Revenue − COGS − SG&A − D&D | 66,766 |
| Other income | `INC_other_income` | Non-operating | 4,144 |
| Interest expense | `INC_interest_expense` | | 996 |
| Taxes | `INC_taxes` | Effective rate 28.26% | 13,810 |
| Net income | `INC_net` | | 56,104 |
| Dividends | `INC_dividends` | | 16,700 |

### Cash Flow & Market Inputs (FY2024)

| Variable | Named Range | Notes | Value |
|---|---|---|---|
| Cash from operations | `CASH_operating` | | $77,514M |
| Cash from investments | `CASH_investments` | | $(22,618)M |
| Share price | `share_price` | Closing Dec 31, 2024 | $103.76 |
| Shares outstanding | `shares_outstanding` | Diluted, FY2024 | 4,298M |
| Cost of capital | `cost_capital` | Estimated WACC | 9.0% |
| Tax rate | `tax_rate` | Effective rate from 10-K | 28.26% |

---

## 3. Assumptions & Constraints

- All figures in millions of USD; calendar fiscal year (Jan 1 – Dec 31).
- Tax rate: 28.26% — effective rate from 10-K (income taxes ÷ pretax income).
- Cost of capital: 9.0% estimated WACC; may be refined via CAPM.
- Share price $103.76 = closing price Dec 31, 2024. Shares outstanding 4,298M diluted.
- Start-of-year values use FY2023 Balance Sheet. Average denominators = arithmetic mean of FY2023 and FY2024.
- Depreciation taken from Income Statement. No off-balance-sheet adjustments applied.

---

## 4. Calculation Flow

### Step 1 — Derived Inputs

- `market_capitalization` = `share_price` × `shares_outstanding` → $445,960M
- `currentYear_ATOI` (after-tax operating income) = `INC_ebit` + (1 − `tax_rate`) × `INC_interest_expense` → $67,481M
- `daily_sales_avg` = `INC_sales` / 365 ; `daily_COGS_avg` = `INC_cost_goods_sold` / 365
- `currentYear_working_capital_net` = `BAL_assets_current_2024` − `BAL_liabilities_current_2024` → $18,660M
- `startYear_total_cap` = `BAL_debt_long_term_2023` + `BAL_equity_shareholders_2023` → $250,021M
- `avg_equity`, `avg_total_assets`, `avg_total_cap` = AVERAGE(start, current) for each

### Steps 2–7 — Ratio Formulas

All 27 ratios and Du Pont components are specified in Section 5 below with named-range pseudocode.

---

## 5. Outputs & Ratio Formulas

| Category | Ratio | Named Range | Formula (Named-Range Pseudocode) |
|---|---|---|---|
| **Performance** | Market Value Added (MVA) | — | `market_capitalization − currentYear_equity` |
| | Market-to-Book | — | `market_capitalization / currentYear_equity` |
| | Economic Value Added (EVA) | — | `currentYear_ATOI − (cost_capital × startYear_total_cap)` |
| **Profitability** | ROA | — | `currentYear_ATOI / startYear_total_assets` |
| | ROC | — | `currentYear_ATOI / startYear_total_capitalization` |
| | ROE | — | `INC_net / startYear_equity` |
| | ROA / ROC / ROE [AVG] | — | Same as above; replace start-year with avg denominators |
| **Efficiency** | Asset Turnover | `RATIO_asset_turnover` | `INC_sales / startYear_total_assets` |
| | Receivables Turnover | — | `INC_sales / startYear_receivables` |
| | Avg Collection Period (days) | — | `startYear_receivables / daily_sales_avg` |
| | Inventory Turnover | — | `INC_cost_goods_sold / startYear_inventory` |
| | Days in Inventory | — | `startYear_inventory / daily_COGS_avg` |
| | Profit Margin | — | `INC_net / INC_sales` |
| | Operating Profit Margin | `RATIO_operating_profit_margin` | `currentYear_ATOI / INC_sales` |
| **Leverage** | Long-Term Debt Ratio | — | `LT_debt / (LT_debt + currentYear_equity)` |
| | LT Debt-Equity Ratio | — | `LT_debt / currentYear_equity` |
| | Total Debt Ratio | — | `currentYear_liabilities_total / currentYear_assets_total` |
| | Times Interest Earned | — | `INC_ebit / INC_interest_expense` |
| | Cash Coverage Ratio | — | `(INC_ebit + INC_depreciation) / INC_interest_expense` |
| | Debt Burden | `RATIO_debt_burden` | `INC_net / currentYear_ATOI` |
| | Leverage Ratio | `RATIO_leverage` | `currentYear_assets_total / currentYear_equity` |
| **Liquidity** | NWC-to-Assets | — | `currentYear_working_capital_net / currentYear_assets_total` |
| | Current Ratio | — | `currentYear_assets_current / currentYear_liabilities_current` |
| | Quick Ratio | — | `(currentYear_cash + BAL_receivables_2024) / current_liabilities` |
| | Cash Ratio | — | `currentYear_cash / currentYear_liabilities_current` |
| **Du Pont** | ROA (Du Pont) | — | `RATIO_asset_turnover × RATIO_operating_profit_margin` |
| | ROE (Du Pont) | — | `RATIO_leverage × RATIO_asset_turnover × RATIO_operating_profit_margin × RATIO_debt_burden` |

> *ATOI = after-tax operating income (`currentYear_ATOI`). `LT_debt` = `currentYear_debt_long_term`. All named ranges reference the course workbook.*

---

## 6. Model Review

**What worked well**
- Named-range architecture made formulas readable and audit-proof across all four worksheets.
- Du Pont ROA matched direct ROA within 0.001 pp, confirming cross-formula consistency.
- Three-tier color key (blue = analyst inputs, yellow = financial data, light yellow = formula outputs) reduced entry errors.

**What to fix**
- Quick Ratio referenced `BAL_receivables_YEAR` — should be `BAL_receivables_2024`. Naming inconsistency to correct.
- Balance check shows a $7,114M discrepancy (Assets vs. Liabilities + Equity) — likely minority interest treatment; needs footnote reconciliation.
- Cash flow inputs were estimated from balance sheet changes rather than sourced directly from the Statement of Cash Flows.

**What would improve the model**
- Peer comparison tab (CVX, BP, SHEL) and multi-year trend (FY2022–2024) for context.
- Formula documentation column and conditional formatting flags for out-of-range ratios.

---

## 7. Limitations & Next Steps

This specification does not incorporate industry peer comparisons, multi-year trend analysis, or off-balance-sheet obligations. The balance sheet discrepancy noted above requires further reconciliation. The next phase (Stage 4) will use this spec as the blueprint for a structured AI prompt, followed by an executive memo interpreting ratio results and providing strategic recommendations for senior management.

---

*University of Hawaiʻi at Mānoa · Shidler College of Business · BUS-314 International Corporate Finance*
