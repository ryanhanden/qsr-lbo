[SEC_Pipeline_README.md](https://github.com/user-attachments/files/31244701/SEC_Pipeline_README.md)
# SEC Financials Pipeline

A command-line tool that pulls standardized financial statements for any public
company straight from the **SEC EDGAR XBRL API**, loads them into a local
**SQLite** database, computes credit and profitability **metrics**, and exports
a formatted **Excel** workbook.

It automates the manual data-gathering behind fundamental analysis: instead of
opening a 10-K and keying revenue, EBITDA, debt, and cash into a spreadsheet
company by company, you run one command and get a clean, queryable dataset.

```
tickers ──► EDGAR API ──► parse & normalize ──► SQLite ──► metrics ──► Excel
```

## Why it exists

Every filer tags the same line item differently in XBRL (Apple files revenue as
`RevenueFromContractWithCustomerExcludingAssessedTax`; other companies use
`Revenues` or `SalesRevenueNet`), and every tag mixes annual, quarterly, and
restated datapoints. This tool resolves all of that into one tidy table per
company, so the analysis starts from clean inputs.

## Install

```bash
pip install -r requirements.txt        # requests, openpyxl
```

## Usage

**Live** (needs internet; the SEC requires a contact email in the User-Agent):

```bash
python sec_pipeline.py AAPL MSFT NVDA --email you@example.com \
    --db output/financials.db --excel output/financials.xlsx
```

**Offline** (no network — parse a company-facts JSON already on disk; used by
the test suite and for a quick demo):

```bash
python sec_pipeline.py --sample sec_financials/sample_data/AAPL_companyfacts_sample.json \
    --db output/financials.db --excel output/financials.xlsx
```

Responses are cached under `.edgar_cache/`, so re-runs don't re-hit the API.

## What you get

**SQLite** — three tidy tables (`companies`, `financials`, `metrics`) built for
ad-hoc SQL:

```sql
SELECT c.ticker, m.fiscal_year, m.value AS net_debt_to_ebitda
FROM metrics m JOIN companies c USING (cik)
WHERE m.metric = 'net_debt_to_ebitda'
ORDER BY c.ticker, m.fiscal_year;
```

**Excel** — one sheet per company: line items on top, derived metrics below,
fiscal years across the columns, values in $mm.

## Line items pulled

Revenue, cost of revenue, gross profit, operating income, net income, D&A, cash
flow from operations, capex, total/current assets, total/current liabilities,
stockholders' equity, cash, and short-/long-term debt.

## Metrics computed

Revenue growth (YoY), gross / operating / net margin, EBITDA and EBITDA margin,
current ratio, net debt, **net debt / EBITDA**, return on equity, free cash flow
(CFO − capex), and FCF margin. Each guards against missing inputs and zero
denominators, returning blanks rather than erroring.

## Design notes

- **Tag fallbacks** — each canonical concept lists its candidate XBRL tags in
  priority order (`sec_financials/concepts.py`); the parser takes the first that
  has data.
- **Annual-only** — keeps `form=10-K`, `fp=FY` datapoints, and for flow items
  additionally checks the period spans ~a full year, which filters out Q4 rows
  some filers mis-tag as `FY`.
- **Restatement handling** — when a fiscal year appears in more than one filing,
  the most recently filed value wins.
- **Polite API use** — descriptive User-Agent with contact email and request
  throttling per SEC fair-access guidance. No API key required; data is public.

## Project layout

```
sec_pipeline.py              CLI entry point / orchestration
sec_financials/
    concepts.py              canonical concepts + XBRL tag fallbacks
    edgar.py                 EDGAR client (ticker->CIK, company facts, caching)
    parse.py                 raw facts -> per-fiscal-year table
    metrics.py               ratios & credit stats
    store.py                 SQLite schema + writes
    exporter.py              formatted Excel export
    sample_data/             bundled Apple sample for offline runs
tests/
    test_pipeline.py         offline tests (parsing, filters, metric math, SQLite)
```

## Tests

```bash
python tests/test_pipeline.py       # no network required
```

---

*Data sourced from the U.S. Securities and Exchange Commission's EDGAR system
(public domain). This tool is for research and educational use.*
