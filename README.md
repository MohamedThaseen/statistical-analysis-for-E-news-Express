# E-news Express: A/B Testing a New Landing Page

A statistical analysis for **E-news Express**, an online news portal, to decide whether a redesigned landing page is better at engaging visitors and converting them into subscribers. The analysis uses hypothesis testing at a **5% significance level** on A/B test data.

## Table of Contents
- [Business Context](#business-context)
- [Objective and Questions](#objective-and-questions)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Results](#results)
- [Notes on the Notebook](#notes-on-the-notebook)
- [Recommendations](#recommendations)
- [Getting Started](#getting-started)
- [Repository Structure](#repository-structure)
- [Tech Stack](#tech-stack)

## Business Context
E-news Express wants to grow its subscriber base. Executives believe the decline in new monthly subscribers compared with last year is because the current landing page's layout and recommended content do not keep visitors engaged long enough to subscribe. The design team built a new page with a new outline and more relevant content.

## Objective and Questions
100 users were randomly chosen and split equally into a **control group** (old page) and a **treatment group** (new page). The analysis answers four questions:

1. Do users spend more time on the new landing page than on the old one?
2. Is the conversion rate for the new page greater than for the old page?
3. Does converted status depend on the preferred language?
4. Is the time spent on the new page the same across different language users?

## Dataset
**100 rows × 6 columns**, with no missing values and no duplicates (`abtest.csv`).

| Column | Description |
|--------|-------------|
| `user_id` | Unique user ID |
| `group` | `control` or `treatment` |
| `landing_page` | `old` or `new` |
| `time_spent_on_the_page` | Minutes spent on the landing page (0.19 to 10.71, mean 5.38) |
| `converted` | Whether the user became a subscriber (`yes` / `no`) |
| `language_preferred` | English, French or Spanish |

The groups are perfectly balanced at 50 users each.

> The dataset file is not included in this repository.

## Methodology
- **Data checks and EDA**: shape, nulls, duplicates, descriptive statistics, count plots, box plots of time spent by group, page and conversion, and a correlation heatmap.
- **Preprocessing**: `user_id` dropped, and categorical variables temporarily label-encoded for summaries and the correlation heatmap, then mapped back for the tests.
- **Hypothesis tests** (α = 0.05), each with a plotted rejection region:

| Question | Test |
|----------|------|
| 1. Time spent, new vs old | Independent two-sample t-test (one-tailed, new > old) |
| 2. Conversion rate, new vs old | Two-proportion z-test (one-tailed, new > old) |
| 3. Conversion vs language | Chi-square test of independence |
| 4. Time spent by language (new page) | One-way ANOVA |

## Results

| Question | Key figures | Decision | Conclusion |
|----------|-------------|----------|------------|
| 2. Conversion rate | Control 42% (26/50), treatment 66% (33/50), z = 2.41, p = 0.008 | Reject H₀ | The new page has a significantly higher conversion rate |
| 3. Conversion vs language | χ² = 3.09, df = 2, p = 0.213 | Fail to reject H₀ | No evidence that conversion depends on preferred language |
| 4. Time by language (new page) | F = 0.854, p = 0.432 | Fail to reject H₀ | No evidence that time spent differs across languages |
| 1. Time spent, new vs old | See note below | | Notebook concludes the new page holds attention longer (about 4.9 vs 6.2 minutes) |

Conversion by language (counts of no / yes): English 11 / 21, French 19 / 15, Spanish 16 / 18.

## Notes on the Notebook
Please review these before publishing, because a reader checking the code against the conclusions will notice them.

- **Question 1 code does not match its conclusion.** The t-test cell compares time spent by **conversion status** (converted vs non-converted: t = 6.87, p < 0.001), not by **landing page** (new vs old). The written conclusion and the quoted group means (4.93 and 6.23 minutes) are about the new-vs-old comparison, but the notebook never computes them. To fix, split `time_spent_on_the_page` by `landing_page` and run `stats.ttest_ind(new, old, alternative='greater')`. The README table marks this row accordingly.
- **Question 2 write-up has a typo.** The observation says "p-value > 0.008, we reject H₀". It should read "p-value (0.008) < 0.05".
- **Q1 observation also lists the Q2 hypotheses** (conversion-rate H₀/H₁) under the "Time Spent" heading.
- **Test assumptions are not checked.** The t-test and ANOVA assume roughly normal data and equal variances (for example via Shapiro-Wilk and Levene's tests), and the notebook does not verify them. With only 50 users per group, this matters.
- **Statistical conclusions on a small sample.** 100 users is a small experiment, so confidence intervals and effect sizes would strengthen the findings.

## Recommendations
- The conversion result (42% to 66%) supports adopting the new landing page, and the effect is significant at the 5% level.
- Conversion and engagement do not differ meaningfully by language, so the new page does not need language-specific redesigns on current evidence.
- Confirm the time-on-page result with the corrected new-vs-old test, then validate with a larger follow-up experiment before a full rollout.
- Track subscriber retention, not only initial conversion, to confirm the new page brings lasting value.

## Getting Started

### Prerequisites
Python 3.9+

### Install
```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
pip install pandas numpy matplotlib seaborn scipy statsmodels jupyter
```

### Data and run
Place `abtest.csv` in a `data/` folder. The notebook was written for Google Colab and reads from Google Drive, so remove the `drive.mount(...)` cell and update the CSV path before running locally.
```bash
jupyter notebook Project_Applied_Statistics_E_news_Express.ipynb
```

## Repository Structure
```
.
├── Project_Applied_Statistics_E_news_Express.ipynb   # Full analysis
├── data/                                              # (not tracked) abtest.csv
└── README.md
```

## Tech Stack
Python, pandas, NumPy, Matplotlib, Seaborn, SciPy, statsmodels.

## Author
Mohamed Rifakath Ali A
