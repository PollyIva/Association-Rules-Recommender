# Association Rules Recommender

**Live demo:** https://pollyiva.github.io/Association-Rules-Recommender/

A browser-only association-rule miner for the **UCI Online Retail** dataset
(17,080 baskets, 3,653 items). It implements Apriori, generates rules in both
directions, and ranks them by support, confidence, and lift — no build step, no
server, no dependencies.

Course project: **HW4, LLM4Rec, HSE University**.

---

## Run it

Open [`index.html`](index.html) in any modern browser, or use the live demo
linked above. Everything runs client-side; `transactions.js` (the dataset)
must sit next to `index.html`.

1. Press **Run tests** — the built-in harness reports **11 passed / 0 failed**
   (baseline on the untouched stubs: 2 passed / 9 pending).
2. Set **minimum support** and **minimum confidence** with the sliders.
3. Press **Run rules**. Click any row to open the detail panel; use
   **Reverse direction (B → A)** to compare the two directions.
4. **Click a column header to sort** that column (click again to flip
   ascending/descending). Works for all eight columns: both rule texts,
   the three counts, support, confidence, and lift.

## What is implemented

The seven `TODO(hw4)` stubs in [`script.js`](script.js):

| Stub | Role |
|---|---|
| `dedupeBasket` | invoice–item basket construction (set semantics, first-appearance order) |
| `countItemset` | number of baskets containing every stock in an itemset |
| `computeSupport` | `count(A∪B) / N` |
| `computeConfidence` | `count(A∪B) / count(A)`, `defined:false` on zero denominator |
| `computeLift` | `confidence / (count(B)/N)`, symmetric |
| `findFrequentItemsets` | level-wise Apriori with downward-closure pruning (posting-list intersection) |
| `generateRules` | both-direction rules from every frequent itemset, confidence filter, lift from global N |

`transactions.js`, `index.html`, and `style.css` are provided and unmodified.

## Dataset

| Property | Value |
|---|---|
| Source | [UCI Online Retail, dataset id 352](https://archive.ics.uci.edu/dataset/352/online+retail) |
| Cleaning | 541,909 raw rows → 384,911 rows; cancellations, non-positive quantities/prices, blanks, guest checkouts, and non-product codes removed |
| Baskets | 17,080 (`InvoiceNo`, ≥ 2 distinct items, not split by customer) |
| Items | 3,653 stock codes (trimmed/upper-cased; canonical description per code) |
| Per-basket timestamp | not exported (grouping key is `InvoiceNo` only) |
| Most frequent item | `85123A` WHITE HANGING HEART T-LIGHT HOLDER — 1,959 / 17,080 = **11.47%** |

Citation: Daqing Chen, Sai Laing Sain, and Kun Guo, "Data mining for the
online retail industry: A case study of RFM model-based customer segmentation
using data mining," 2012. Dataset: Chen, D. (2015), *Online Retail* [Dataset],
UCI Machine Learning Repository, DOI
[10.24432/C5BW33](https://doi.org/10.24432/C5BW33).

## Key results

Final operating point: **support ≥ 1%, confidence ≥ 30%** → 1,219 frequent
itemsets and **950 rules** (all with lift > 1).

| Support | Confidence | Frequent itemsets | Rules |
|---:|---:|---:|---:|
| 0.5% | 30% | 5,255 | 10,723 |
| **1%** | **30%** | **1,219** | **950** |
| 3% | 30% | 115 | 12 |
| 1% | 60% | 1,219 | 238 |
| 1% | 90% | 1,219 | 18 |
| 3% | 60% | 115 | 5 |

Two rules worth quoting (full analysis in the course report):

- **Useful:** `GREEN REGENCY TEACUP → ROSES REGENCY TEACUP` —
  541/691/780, support 3.17%, confidence 78.29%, **lift 17.1440**
  (consequent base rate only 4.57%).
- **Misleading (reject):** `HOME BUILDING BLOCK WORD → 85123A WHITE HANGING
  HEART` — 209/690/1959, support 1.22%, confidence 30.29%, **lift 2.6409**.
  Since the consequent sits in 11.47% of baskets, any rule into it at ≥ 30%
  confidence has lift ≥ 0.30/0.1147 ≈ 2.62 — this one is at that floor, so it
  passed almost automatically.

## Verification

- Built-in harness: **11 / 11 checks pass** (fixtures cover lift = 1, lift > 1,
  lift < 1, direction asymmetry, dedup, zero-denominator guards, both miners).
- Hand-computed from raw counts:
  `23171 → 23172`: support = 202/17080 = 1.18%, confidence = 202/269 = 75.09%,
  lift = 0.750929 / (224/17080) = **57.2584** — matches the page exactly;
  reversed direction gives confidence 90.18% with the **same** lift.
- Every row of the exported rule tables (1,200 rows across three settings) was
  re-derived from raw counts by an independent recomputation: **0 discrepancies**.

## Repository structure

| File | Role |
|---|---|
| `index.html` | page structure (provided) |
| `style.css` | styling (provided) |
| `script.js` | implementation: Apriori, metrics, rule generation, sortable results table, test harness |
| `transactions.js` | dictionary-encoded dataset (provided, do not modify) |
| `assignment.md` | original HW4 assignment specification |
| `readme.md` | this file |
| `report.pdf` | the course report (analysis, results, verification) |

Generated artefacts (analysis harness, exports, screenshots, `report.md` source
of the submitted PDF) are intentionally not committed.
