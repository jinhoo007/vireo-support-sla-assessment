# Vireo Audio — Support SLA Analysis

AI-assisted analysis tool for identifying weekly first-response SLA breaches by **agent and shift**, with deterministic business-impact calculations and AI-assisted ticket theme analysis.

## Business Objective

Vireo Audio's Support Operations team needs a reliable weekly view of first-response SLA performance to identify where breaches are concentrated and support focused operational conversations.

The tool answers three core questions:

1. How many tickets breached the first-response SLA?
2. Which agents and shifts account for the breaches?
3. What is the associated automatic store-credit exposure?

AI is used only as an assistive layer for analyzing free-text ticket themes. SLA calculations and financial metrics remain deterministic and reproducible.

---

## Key Results

Analysis of **11,200 unique tickets** produced:

| Metric | Result |
|---|---:|
| Unique tickets analyzed | 11,200 |
| SLA passes | 8,760 |
| SLA breaches | 2,440 |
| Overall breach rate | **21.79%** |
| Automatic credit exposure | **₹854,000** |
| Morning-shift breaches | 2,018 |
| Morning share of all breaches | **82.70%** |
| Morning breach rate | **32.22%** |

Every SLA breach carries an automatic ₹350 store-credit exposure under the applicable support policy.

---

## Solution Approach

```text
Tickets + Agent Roster
          │
          ▼
   Data Validation
          │
          ▼
 Duplicate Handling
          │
          ▼
 UTC → IST Conversion
          │
          ▼
 Historical Roster Join
          │
          ▼
 Channel-specific SLA Engine
          │
          ▼
 Agent + Shift Analysis
          │
          ├───────────────► Weekly SLA Reports
          │
          ▼
 AI-assisted Ticket Analysis
          │
          ▼
 Operational Theme Evidence
```

### Deterministic layer

Python handles:

- Data validation
- Duplicate ticket handling
- Timestamp parsing
- UTC → IST conversion
- Historical agent/shift assignment
- Channel-specific SLA targets
- First-response SLA breach calculation
- Weekly aggregation
- Agent-level analysis
- Shift-level analysis
- Store-credit exposure
- Reconciliation checks

### AI-assisted layer

Gemini is used to classify a controlled sample of ticket text into:

- Primary issue
- Complexity
- Operational factor
- Likely breach driver

The AI layer is **not** used to determine whether a ticket breached SLA.

---

## SLA Policy

The analysis follows the supplied Support Policy v3.2:

| Channel | First-response target |
|---|---:|
| Chat | 15 minutes |
| Voice | 2 hours |
| Social | 4 hours |
| Email | 8 hours |

A ticket is considered a breach only when:

> First human response time **>** applicable SLA target.

An exact response time equal to the target is treated as a **PASS**.

---

## AI Evaluation

A controlled 40-ticket sample was used to evaluate the AI-assisted classification component.

| Metric | Result |
|---|---:|
| Tickets submitted to AI | 40 |
| Successful structured classifications | 14 |
| API failures | 26 |
| Technical completion rate | 35% |

The 26 failed requests were primarily caused by Gemini API rate/quota availability rather than JSON parsing or schema-validation failures.

Therefore, **35% is not reported as model accuracy**.

The 14 successful classifications were schema-validated and manually inspected as qualitative evidence.

The AI-reviewed sample surfaced:

- **Delivery & Shipping** as the most frequent primary issue
- **Technical Troubleshooting** as the most frequent operational factor
- **Delivery / Logistics Dependency** as another recurring operational factor

These findings are descriptive signals for investigation and **do not establish causation**.

---

## Outputs

The notebook generates:

### Weekly Agent SLA Report

Contains:

- Week
- Agent
- Shift
- Ticket volume
- SLA breaches
- Breach rate
- Credit exposure

### Weekly Shift SLA Report

Contains:

- Week
- Shift
- Ticket volume
- SLA breaches
- Breach rate
- Credit exposure

### Excel Business Report

The notebook also creates:

`vireo_weekly_sla_report.xlsx`

with:

- Executive Summary
- Weekly Agent SLA
- Weekly Shift SLA

---

## Project Structure

```text
vireo-support-sla-assessment/
│
├── vireo_support_sla_analysis.ipynb
├── README.md
├── LICENSE
├── .gitignore
│
├── docs/
│   └── memo-to-neha.md
│
└── reports/
```

Raw assessment data and generated private outputs are intentionally excluded from the repository.

---

## How to Run

### Requirements

- Python 3.10+
- Google Colab or Jupyter Notebook
- Gemini API key for the AI-assisted classification section

### Option 1 — Google Colab

Open:

`vireo_support_sla_analysis.ipynb`

in Google Colab.

Upload the required input CSV files when prompted.

The notebook installs the required Python dependencies automatically.

### Option 2 — Local Jupyter

Clone the repository and open the notebook:

```bash
git clone <repository-url>
cd vireo-support-sla-assessment
```

Then launch Jupyter:

```bash
jupyter notebook
```

Open:

```text
vireo_support_sla_analysis.ipynb
```

---

## Reproducibility and Validation

The solution includes checks for:

- Required input columns
- Duplicate ticket IDs
- Timestamp validity
- First-response ordering
- Historical roster matching
- Missing SLA labels
- AI JSON schema validity
- SLA/financial reconciliation

The final credit exposure independently reconciles to:

```text
2,440 breaches × ₹350
= ₹854,000
```

---

## Important Limitations

### AI API availability

The Gemini API evaluation was affected by request quota/rate limits. The failure is documented rather than hidden.

### AI classification

The AI output is used for qualitative ticket-theme analysis. It is not treated as a causal model or ground-truth root-cause system.

### Breach volume vs. breach rate

Agent and shift comparisons consider both ticket volume and breach rate. High breach volume alone does not imply poor performance without considering workload.

### Tier comparison

Tier 2 is not compared with Tier 1 on ticket volume because their operating models differ.

### Causality

The analysis does not claim that SLA breaches caused changes in P&L credits, CSAT, or other business outcomes.

---

## Technology

- Python
- Pandas
- NumPy
- Matplotlib
- OpenPyXL
- Google Gemini API
- Google Colab / Jupyter

The solution intentionally avoids unnecessary frameworks such as LangChain, LangGraph, RAG pipelines, or multi-agent systems because they do not add value to this specific business requirement.

---

## AI Usage Disclosure

AI assistance was used during development for:

- Designing the ticket-classification schema
- Developing the Gemini classification prompt
- Implementing structured JSON validation
- Debugging API integration and retry behavior
- Reviewing implementation decisions

Gemini was used in the final solution for free-text ticket classification.

The AI component was deliberately kept separate from deterministic SLA and financial calculations.

---

## License

This project is released under the MIT License.
