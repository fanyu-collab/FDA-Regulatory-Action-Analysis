# FDA Regulatory Action Analysis

**Does FDA enforcement go where product-safety problems actually happen?**
This project links FDA recall data with FDA compliance actions (warning letters, seizures and injunctions) at the facility level to test whether enforcement follows risk.

**Team A06 · BA780 (Boston University Questrom)** · Malak Taoujni, Filip Handjiski, Fan-yu Chiu, Rislyn Raja, Aryan Bajaj

![Share of recall events rated Class I, 2013–2025](images/class1_share_trend.png)

---

## Overview

The FDA has a limited number of inspectors, so it has to choose where to focus enforcement. Recalls and compliance actions are usually studied separately. We joined them by facility ID (**FEI Number**) to answer 15 questions about recall trends, severity, causes, enforcement patterns, facility-level risk and geography.

The analysis is aimed at three audiences:
- **Regulators** deciding where to inspect
- **Manufacturers and compliance teams** managing compliance risk
- **Consumers and policymakers** tracking whether product safety is improving

## Key Findings

| Finding | Evidence |
|---|---|
| **Recalls are getting more dangerous** | Class I share of recall events rose from **10.4% (2013) to 19.6% (2025)**, and the Class I count rose from 380 to 412 even as total recalls fell |
| **Food drives the most dangerous recalls** | Food/Cosmetics = **61%** of all Class I recalls; undeclared allergens, Salmonella and Listeria cause **58%** of Class I recalls |
| **Enforcement follows recall volume** | Action rate rises from **13.5%** of facilities with 1 recall event to **46.4%** with 100+ |
| **…but barely reflects severity** | 20.0% of facilities with a Class I recall received an action vs. 15.3% with only Class II/III |
| **Enforcement arrives late** | Recalls peak in the year of a facility's first action (0.54 per facility), then fall **63%** within three years |
| **Devices are under-enforced** | Devices = **40.5%** of non-tobacco recall events but only **15.2%** of actions |
| **Non-tobacco enforcement has shrunk** | Warning letters 779 → 500 and seizures/injunctions 48 → 2 (2011 → 2025) |
| **State-level gaps** | Virginia, Minnesota, Iowa and Georgia have many repeat-recall facilities but few actions |

<p align="center">
  <img src="images/action_rate_by_recall_count.png" width="48%" alt="Share of facilities with an FDA action by number of recalls">
  <img src="images/recalls_around_first_action.png" width="48%" alt="Average recall events per facility around first FDA action">
</p>

## Recommendations

1. **Early-warning sign:** flag facilities with rising or Class I recalls so the FDA acts before the peak. *(Impact: High · Feasibility: High)*
2. **Food pathogens and allergens:** focus food inspections on the problems behind 58% of Class I recalls. *(Impact: High · Feasibility: Medium)*
3. **Device oversight:** close the gap between devices' share of recalls and share of actions. *(Impact: High · Feasibility: Medium)*
4. **High-recall facilities:** review the 53.6% of facilities with 100+ recall events that were never disciplined. *(Impact: Medium · Feasibility: High)*

## Data

| Dataset | Coverage | Size | Source |
|---|---|---|---|
| FDA Recalls | June 2012 – Sept 2026 | 104,083 product rows / 37,623 recall events (~69 MB) | [FDA Data Dashboard – Recalls](https://datadashboard.fda.gov/oii/cd/recalls.htm) |
| FDA Compliance Actions | 2008 – Sept 2026 | 177,252 actions (~17 MB) | [FDA Data Dashboard – Compliance Actions](https://datadashboard.fda.gov/oii/cd/complianceactions.htm) |

- **License:** public domain (U.S. government data)
- **Link key:** `FEI Number` (unique FDA facility ID). 1,724 facilities appear in both datasets.
- The notebook loads both CSVs directly from shared Google Drive links, so no local download is needed.

### Key fields

| Dataset | Column | Meaning |
|---|---|---|
| Both | `FEI Number` | Unique FDA facility ID (links the datasets) |
| Recalls | `Product Classification` / `Event Classification` | Severity: Class I (most serious), II, III |
| Recalls | `Event ID` | One recall event (can cover several products) |
| Recalls | `Reason for Recall` | Free-text cause, grouped by keyword in the analysis |
| Compliance | `Action Type` | Warning Letter, Seizure, Injunction |
| Both | `Product Type` | Devices, Food/Cosmetics, Drugs, Biologics, Veterinary, Tobacco |
| Both | `Year` | Derived from the classification / action date |

## Methods

**Data cleaning**
- Replaced the FDA's `"-"` placeholders with `NaN` to expose hidden missing values
- Dropped 30 recall rows with no FEI Number (<0.03%) and converted FEI to integer
- Filled missing states with `"No US State"` (mostly foreign firms) and other gaps with `"Unknown"`
- Converted dates to `datetime` and added a `Year` column; no duplicate rows found
- Counted each **recall event** (`Event ID`) once instead of product rows
- Kept high-recall outliers (max 317 events at one facility vs. median 1) because they are real repeat offenders

**Analysis**
- Built a facility-level table (`facility_df`) of recall and action counts joined on FEI Number
- Excluded tobacco (94% of compliance actions) when comparing product types, states and enforcement trends
- Used full calendar years for trends to avoid partial-year distortion
- Grouped free-text recall reasons with keyword rules
- Used medians, distributions and repeat-recall shares to handle heavily skewed counts
- Event-study view of recalls three years before and after a facility's first action (Q13)
- Percentile-rank "enforcement gap" between repeat-recall rate and action rate by state (Q15)

## Questions Answered

**Part 1: Team Assignment**

| # | Question |
|---|---|
| Q1 | How has the number of FDA recalls changed from 2012 to 2026? |
| Q2 | Do the product types with the most recalls also have the most FDA actions? |
| Q3 | Do the states with the most recalls also have the most FDA actions? |
| Q4 | Do facilities that ever had a compliance action have more recalls? |
| Q5 | Which product types get the harshest actions (seizures and injunctions)? |

**Part 2: Deeper Analysis**

| Section | # | Question |
|---|---|---|
| Severity & causes | Q6 | Are recalls becoming more dangerous over time? |
| | Q7 | Which product types produce the most dangerous recalls? |
| | Q8 | What are the most common reasons for recalls? |
| | Q9 | Which product types does each reason come from, and what drives Class I recalls? |
| Enforcement | Q10 | How has FDA enforcement changed over time, outside tobacco? |
| Facility risk | Q11 | Are facilities with more recalls more likely to receive an action? |
| | Q12 | Are facilities with a Class I recall more likely to be disciplined? |
| | Q13 | What happens to a facility's recalls before and after its first action? |
| Geography | Q14 | Which kinds of recall problems does each state specialize in? |
| | Q15 | Which states are under-enforced relative to their recall burden? |

![Enforcement gap by state](images/enforcement_gap_by_state.png)

## Project Structure

```
├── A06_FDA_Regulatory_Action_Analysis.ipynb   # full analysis (cleaning, Q1–Q15, conclusions)
├── images/                                    # key charts exported from the notebook
│   ├── class1_share_trend.png
│   ├── class1_by_product_type.png
│   ├── action_rate_by_recall_count.png
│   ├── recalls_around_first_action.png
│   └── enforcement_gap_by_state.png
└── README.md
```

## Getting Started

**Option 1: Google Colab (recommended)**
Open the notebook in [Google Colab](https://colab.research.google.com/) and select **Runtime → Run all**. The data loads automatically from Google Drive.

**Option 2: Run locally**
```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
pip install pandas numpy matplotlib jupyter
jupyter notebook A06_FDA_Regulatory_Action_Analysis.ipynb
```

Requires Python 3 with `pandas`, `numpy` and `matplotlib`, plus an internet connection to download the two CSVs.

## Limitations

- **Correlation, not causation.** There is no direct link between a specific recall and a specific action, so the results describe associations and timing.
- **Approximate reason groups.** Recall reasons are free text; 36.8% of recall events did not match a keyword group.
- **Facility size isn't controlled for.** Large facilities make more products, so recall counts partly reflect size.
- **Headquarters state.** Firms are counted by headquarters state, which may not be where a product was made or sold.
- **Classification practices.** Severity is assigned by the FDA, so changes in how it classifies recalls could affect trends.

**Next steps:** add FDA inspection and facility-size data, and back-test the early-warning sign on past years.

## References

- U.S. Food and Drug Administration. [FDA Data Dashboard – Recalls](https://datadashboard.fda.gov/oii/cd/recalls.htm) (accessed Sept 2026)
- U.S. Food and Drug Administration. [FDA Data Dashboard – Compliance Actions](https://datadashboard.fda.gov/oii/cd/complianceactions.htm) (accessed Sept 2026)
- U.S. Food and Drug Administration. [Recalls Background and Definitions](https://www.fda.gov/safety/industry-guidance-recalls/recalls-background-and-definitions)

## AI Disclosure

Claude (Anthropic) was used for brainstorming questions, structuring tables, code review and debugging. The team reviewed, edited and validated all AI-assisted content and confirmed every reported number against the notebook output. See the notebook's *Generative AI Disclosure* section for details.
