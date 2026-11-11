# Restaurant Brands International (NYSE: QSR): Leveraged Buyout Model

**File:** `QSR_LBO_Model.xlsx`

An illustrative five-year leveraged buyout of Restaurant Brands International, the franchisor behind Burger King, Tim Hortons, Popeyes, and Firehouse Subs. Entry revenue and adjusted EBITDA are anchored to approximate FY2024 reported figures. All drivers live on one Assumptions tab, and every other cell is formula-driven.

## Transaction

| | |
|---|---|
| Entry adjusted EBITDA | ~$2.8B |
| Entry multiple | 13.0x EV / EBITDA |
| Entry enterprise value | $36.4B |
| New debt (Term Loan B + Senior Notes) | $16.8B (6.0x EBITDA) |
| Sponsor equity | $20.3B (55% of sources) |

## Model structure

| Tab | What it does |
|---|---|
| Assumptions | Transaction terms, Sources & Uses, debt terms, operating drivers, exit assumptions |
| Model | Five-year operating build, free cash flow, and a tranched debt schedule (Term Loan B with 1% mandatory amortization and a 75% cash sweep, Senior Notes, and a revolver) |
| Returns | Exit valuation, equity value, MOIC and IRR, and a returns bridge splitting value creation into EBITDA growth and multiple change |
| Summary | Key outputs and entry-by-exit multiple sensitivity tables for IRR and MOIC |

## Base-case results

- **MOIC:** 1.75x
- **IRR (5-year):** 11.9%
- **Exit multiple:** 12.0x (1.0x contraction from entry)
- **Deleveraging:** total debt / EBITDA falls from 6.0x at entry to about 3.0x by Year 5

The sensitivity tables show how much the return depends on price: buying at 11.0x instead of 13.0x, with the same 12.0x exit, lifts the IRR to about 19%.

## Design notes

- Interest is calculated on beginning-of-period balances, so the model has no circular references.
- Sensitivity tables hold Year-5 EBITDA, net debt, new debt, and fees constant and flex only the entry and exit multiples.

*Illustrative model built for learning and portfolio purposes. Not investment advice.*
