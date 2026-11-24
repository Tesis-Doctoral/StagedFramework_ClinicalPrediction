# Stage S10 — from classification to action: severe maternal morbidity, MIMIC-IV (M2)

Spanish version: [`e10_action_m2_smm_es.md`](e10_action_m2_smm_es.md). This document uses only the aggregate outputs already published in the article's repository ([MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus): `data/outputs_main.txt`, `data/outputs_race_subgroups.txt`, `data/outputs_escalation.txt`, `data/outputs_g4_fitted_models.txt`); no new analysis was run and that repository was not changed. Figures from the repeated cross-validation grouped by patient: 19,383 deliveries, 445 with SMM (2.3%).

## 1. Class and use

The model is an **early identifier**: at 24 hours after admission it recognizes patients with SMM well (AUROC 0.791; 0.767–0.816), but much of what it recognizes was already under way (in the landmark population the AUROC falls to 0.719). Its use is therefore not prevention but **surveillance and prioritization of clinical review**:

- a daily list, 24 hours after admission, of the highest-risk patients, so that the obstetric team reviews their record, tests and plan first;
- support for the audit of severe maternal morbidity cases (identifying in time the cases that must be reported and analyzed).

It should not be used to decide preventive interventions: the model does not anticipate morbidity enough for that.

## 2. Operating point and workload

| Operating point | Reviews per 100 deliveries | Sensitivity | PPV | Reviews per SMM case found |
|---|---|---|---|---|
| **Top 5% of risk** | **5** | **0.357 (0.320–0.398)** | **0.164** | **6.1** |
| Top 10% of risk | 10 | 0.514 (0.472–0.557) | 0.118 | 8.5 |

**Recommendation: the top 5% of risk** (the operating point of the article). Reviewing 5 of every 100 deliveries finds one in three SMM cases with about 6 reviews per case. With more capacity, the top 10% finds half of the cases with about 8 or 9 reviews per case. Net benefit exceeds that of reviewing everyone from a risk threshold of 1% (0.0142 vs 0.0131), and the difference grows with the threshold (2%: 0.0114 vs 0.0030).

**Where it adds value:** almost all its signal lies in patients without ICU admission in the first 24 hours (AUROC 0.749). Among those already admitted to the ICU (115 deliveries, 90 with SMM), the model barely discriminates (0.610): those patients are already identified.

**Original objective of the thesis** (sensitivity and specificity above 0.75): with an AUROC of 0.79 the best reachable pair is about 0.72 (binormal approximation); not met, though close.

## 3. Equity: performance by race and ethnicity (XGBoost)

| Group | Deliveries | Cases | AUROC | Observed/expected |
|---|---|---|---|---|
| White | 8,694 | 179 | 0.800 (0.761–0.834) | 0.95 (0.83–1.08) |
| Black | 4,444 | 115 | 0.770 (0.717–0.826) | 0.98 (0.84–1.13) |
| Hispanic or Latina | 1,913 | 30 | 0.744 (0.657–0.827) | 0.82 (0.54–1.15) |
| Asian | 1,792 | 50 | 0.796 (0.722–0.855) | 1.24 (0.94–1.56) |
| Other | 1,616 | 37 | 0.822 (0.755–0.888) | 1.02 (0.74–1.33) |
| Unknown or declined | 924 | 34 | 0.799 (0.711–0.885) | 1.32 (0.91–1.79) |

- Discrimination is similar across groups; the intervals overlap. The Hispanic group has the lowest AUROC and a tendency to overestimate risk, with few cases (30).
- Removing race from the model does not change overall discrimination (difference 0.000) but worsens calibration in the Hispanic (O/E 0.72) and Asian (1.39) groups. Keeping it improves calibration by group.
- In the landmark population discrimination falls more for Black patients (0.689) than for White patients (0.741): the model is less useful to anticipate SMM in that group. This is an explicit caution for implementation.

## 4. The path to a predictor: escalation of care (return R2)

Escalation of care after 24 hours (ICU admission, mechanical ventilation, a procedure-based SMM indicator or death) did meet the criteria of a predictor (AUROC 0.763; landmark 0.761; temporal 0.714), but with 101 events it does not pass gate G3. Its top 10% of risk holds 46.5% of the escalations, with about 41 reviews per escalation found. This is the line to validate with multicenter data: if confirmed with more events, it would allow anticipating deterioration rather than only recognizing morbidity.

## 5. Conditions to implement it

1. **Recalibration is mandatory.** In later years the model underestimated risk (slope 0.83; O/E 1.34). Before using it in another institution or period it must be recalibrated with local data, and calibration must be monitored every year.
2. **External validation.** It was developed in a single high-complexity hospital (Beth Israel Deaconess, Boston). It must be validated in hospitals with another population and organization of care, for example in Colombia.
3. **Data at 24 hours.** It needs age, the admission registration variables, hemoglobin and creatinine available at 24 hours. If the tests were not ordered, the model loses part of its information.
4. **Use as a review list**, not as an automatic alarm or a treatment criterion.

## 6. Limitations

- SMM is an administrative classification based on diagnosis codes for the whole hospitalization; its timing could be dated in only part of the cases.
- A single institution, data from 2008 to 2022.
- Detection bias (criterion C3): renal failure is predicted mostly in patients whose creatinine was measured.
