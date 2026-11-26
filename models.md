# Models that apply the framework

Catalogue of every model designed in the thesis, including those that did not pass a gate, with the link to its repository and the status of each stage and gate. Updated on 2026-11-24; model numbering revised that day (equivalences in section 10). Spanish version: [`models_es.md`](models_es.md). The framework is described in [`README.md`](README.md).

**Status conventions.** **Yes**: the task was done. **In progress**: started, not finished. **Pending**: not done yet, but will be. **Not performed**: will not be done because the model stopped at an earlier gate. **Not applicable**: the criterion does not apply to that outcome. Gates show whether the model **passes** or **fails**, with the indicators obtained and the decision taken. AUROC with its 95% CI in parentheses.

**Result classes.** After gate G4: **predictor**, **early identifier** and **insufficient signal** (*neither* in the framework figure and in the Cureus article). Before G4: **exploratory** (fails G3 and leads to another model through R1) and **feasibility** (fails G3 with no rung or useful return left). A **derived model** comes from a return R1 (another rung of the outcome ladder) and has its own two returns; a **verification model** is built to test a finding of another model.

## 1. Summary

| Id | Model | Outcome | Source | Prediction point | Origin | Reached | Result | Repository |
|---|---|---|---|---|---|---|---|---|
| M1 | Maternal death | Death in the delivery hospitalization | MIMIC-IV v3.1 | Admission + 24 h | — | G3 | **Exploratory**: fails G3 (3 events); leads to M2 through R1 | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M2 | Severe maternal morbidity (SMM) | 20 CDC indicators | MIMIC-IV v3.1 | Admission + 24 h | Derived from M1 (R1) | S11 | **Early identifier** (AUROC 0.791); returns R4 (early identifier) and R2 (escalation: exploratory signal of prediction). Cureus article | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M3 | Fetal death | Fetal death (maternal ICD codes) | MIMIC-IV v3.1 | Admission + 6 h; return R3: antenatal (information from 7 days before or earlier) | — | G3 | **Feasibility**: at admission + 6 h it stopped at S6 (73 of 129 deaths already present); with R3 (antenatal) it fails G3 (negative Cox-Snell R²) | [PerinatalMortality_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalMortality_MIMIC-IV) |
| M4 | Neonatal mortality | Death at 0 to 27 days | NCHS (CDC), linked births and infant deaths, cohorts 2016–2023 | Birth + 5 min | — | G4 | **Predictor** (XGBoost, AUROC 0.964; at 48 h 0.932, drop 0.032; temporal 0.961, slope 1.01) | [NeonatalMortality_NCHS](https://github.com/Tesis-Doctoral/NeonatalMortality_NCHS) |
| M5 | Perinatal mortality | Fetal death at 22 weeks or more or neonatal death at 0 to 6 days | NCHS (CDC) | Antenatal (option A) | — | G4 | **Insufficient signal, closed on 2026-11-24** (XGBoost, clean AUROC 0.723; 0.7455 with the form signal carried by median imputation; threshold 0.75; meets C4). Returns used: R4 (28 weeks or more, 0.676; detail in appendix) and version 2. G2 check (2026-11-24): median imputation carried form-type signal; with random-draw imputation the AUROC is 0.723 | [PerinatalMortality_NCHS](https://github.com/Tesis-Doctoral/PerinatalMortality_NCHS) |
| M6 | Fetal death at 22 weeks or more | Fetal death vs live birth | NCHS (CDC) | Antenatal | Verification of M5 (G2) | G4 | **Insufficient signal** (XGBoost, AUROC 0.680; temporal 0.673). Validates gate G2: without the form-type signal, fetal death falls from 0.760 to 0.680 | [FetalDeath_NCHS](https://github.com/Tesis-Doctoral/FetalDeath_NCHS) |

Repository of this framework: [StagedFramework_ClinicalPrediction](https://github.com/Tesis-Doctoral/StagedFramework_ClinicalPrediction). All repositories are in the [Tesis-Doctoral](https://github.com/Tesis-Doctoral) organization.

## 2. Matrix of stages and gates

| Model | S1 | S2 | S3 | S4 | **G1** | S5 | S6 | **G2** | S7 | **G3** | S8 | S9 | **G4** | S10 | S11 |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| M1 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |  |  | ✅ |
| M2 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | 🟡 | ✅ | ✅ |
| M2 · R4 |  |  |  |  |  |  |  |  | ✅ |  | ✅ | ✅ | 🟡 |  | ✅ |
| M2 · R2 |  |  | ✅ | ✅ | ✅ |  |  | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ |  | ✅ |
| M2 · R2 reduced |  |  |  |  |  |  |  |  | ✅ | ✅ | ✅ | ✅ | ❌ |  | ✅ |
| M3 | ✅ | ✅ | ✅ | ✅ | ✅ |  | ✅ |  |  |  |  |  |  |  |  |
| M3 · R3 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |  |  |  |
| M4 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |  |
| M5 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |
| M6 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |

**Legend**

- **Stages (S):** ✅ the task was done; blank, not done (pending, in progress, or will not be done because the model stopped earlier).
- **Gates (G):** ✅ passes; ❌ fails.
- **At gate G4:** ✅ predictor; 🟡 **early identifier**; ❌ **insufficient signal**.
- **M2 · R2:** the ✅ at G4 means criteria C1 and C2 were met (C4 not assessable for lack of events) although gate G3 was not passed.
- **M2 · R2 reduced:** the ❌ at G4 means the primary model fell just below the C2 threshold and C4 could not be assessed (exploratory signal).
- **Return rows (R4, R2, R3):** mark only the stages that were redone; the others are inherited from the main model.
- Details and indicators for each cell are in the following sections.

## 3. How they relate

```
Maternal slot       M1 Maternal death ──R1 (derived)──► M2 SMM ──R4──► extended laboratory tests
                                                           └─R2──► escalation of care ──► sample-size reduction
Fetal slot          M3 Fetal death (MIMIC-IV): admission + 6 h ──R3──► antenatal
Perinatal slot      M5 Perinatal mortality (NCHS) ──R4──► 28 weeks or more (appendix)
                                               └─version 2──► + four pregnancy conditions (2b: + six variables)
                    M5 ──G2 verification──► M6 Fetal death (NCHS)
Neonatal slot       M4 Neonatal mortality (NCHS)
```

## 4. M1. Maternal death (MIMIC-IV)

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | MIMIC-IV v3.1, downloaded 2025-04-18 | Shared with M2 |
| S2 Universe and population | Yes | 25,736 obstetric admissions; 19,383 eligible deliveries at 24 h | Shared with M2 |
| S3 Outcome definition | Yes | Death in the same admission: 10 in the universe, 3 among eligible deliveries | Ideal outcome (first rung) |
| S4 Count audit | Yes | 3 events | — |
| G1 Definition | Passes | Counts reproduced | — |
| S5 to S7 | Yes | Shared with M2 (32 candidate parameters) | — |
| G2 Leakage | Passes | Shared with M2 | — |
| **G3 Sample size** | **Fails** | With 32 parameters, the Riley criteria required at least 33 deaths even with the most optimistic assumed R²; there were 3 | **Feasibility analysis; return R1 to the next rung: SMM (M2)** |
| S8 to S11, G4 | Not performed | — | No model is fitted; reported as feasibility within M2 |

## 5. M2. Severe maternal morbidity at 24 hours (MIMIC-IV)

### Main model

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | MIMIC-IV v3.1; fixed seed, versions and reference counts | — |
| S2 Universe and population | Yes | Universe: 25,736 admissions (15,084 patients); 19,425 deliveries; 19,383 eligible at 24 h (13,710 patients) | Delivery hospitalizations still ongoing at 24 h |
| S3 Outcome definition | Yes | 445 events (2.30%). Timing: 150 present at the prediction point (33.7%), 10 indeterminate, 114 documented incident (25.6%), 171 undated (38.4%) | At least 1 of 20 CDC indicators; sensitivity with 2 or more |
| S4 Count audit | Yes | Cohort flow, timing classes, death–SMM overlap | — |
| G1 Definition | Passes | `EXPECTED` check in every notebook | — |
| S5 Unit of analysis | Yes | 45% of patients with more than one admission | Delivery hospitalization; patient as group |
| S6 Prediction point | Yes | — | Admission + 24 h |
| G2 Leakage | Passes | Discharge diagnosis groups removed; partitions grouped by patient | — |
| S7 Predictors | Yes | Age, 5 registration variables, hemoglobin, creatinine and creatinine-missing indicator; 32 parameters; 10% coverage rule | — |
| G3 Sample size | Passes | Planning (assumed Cox-Snell R² 0.030, 15% of the maximum): 9,614 admissions and 221 events required. Reassessment (observed out-of-fold R² 0.0311): 9,112 and 209. Available: 19,383 and 445; 32 parameters (columns after one-hot encoding, no intercept); supports up to 68 | Primary learner: XGBoost |
| S8 Modeling | Yes | XGBoost: AUROC 0.791 (0.767–0.816), well calibrated | — |
| S9 Sensitivity | Yes | Definitions, populations, laboratory tests, ICU variables, resampling | — |
| G4 C1 Events present | Not met | On the fitted model: landmark AUROC 0.719 (0.686–0.750); drop 0.072 (0.056–0.088); keeps 40% (30–51%) of the AUPRC gain | — |
| G4 C2 Outcome with time | Met | On the fitted model: documented incident 0.765 (0.713–0.816); strict incident 0.784 (0.721–0.843). Refitted in each subset it was borderline (0.745) | — |
| G4 C3 Detection bias | Not met | Renal failure: AUROC 0.850 (0.822–0.872) overall, 0.628 (0.584–0.671) among tested patients | — |
| G4 C4 Stability | Not met | Temporal AUROC 0.747 (0.707–0.783); calibration slope 0.83 (0.71–0.97); observed/expected 1.34 (1.15–1.52); development period passes G3. Slope tolerance band 0.90–1.10 (sensitivity): not met | — |
| **G4 Classification** | **Early identifier** | Fails C1, C3 and C4; meets C2 | Returns R4 and R2 |
| S10 Action | Yes | Flagging the top 5%: about 970 alerts, positive predictive value 16.4%, about 6 reviews per case | Surveillance and prioritization, not prevention ([`docs/e10_action_m2_smm.md`](docs/e10_action_m2_smm.md)) |
| S11 Reporting | Yes | Manuscript and TRIPOD+AI checklist | — |

### Returns and reduction

| Step | Done | Indicators | Decision |
|---|---|---|---|
| R4 Extended laboratory tests (10 tests with missingness indicators) | Yes | AUROC 0.821 (0.799–0.844); landmark 0.740, drop 0.081 (0.066–0.098), 29% of the gain kept; C2 met (0.763; 0.776); C3: 0.861 falls to 0.643; C4: temporal landmark AUROC 0.697, slope 0.73 (0.56–0.90) | **Remains an early identifier**: the gain came from which tests were ordered |
| R2 Escalation of care after 24 h | Yes | 101 events in 19,268 deliveries (0.52%); AUROC 0.763 (0.709–0.808); 46.5% of events in the top 10%; C1 met (landmark 0.765); C2 met; C3 not applicable; C4 **not assessable**: 46 events in the development period, G3 requires 155. Components: ICU 0.770, ventilation 0.758, procedure-based indicator 0.702 | **Fails G3**: planning 30,120 admissions and 158 events; reassessment (R² 0.0077) 37,098 and 194; supports 16 of 32 parameters |
| Sample-size reduction (13 parameters) | Yes | G3 **passes**: planning 12,236 and 64; reassessment (R² 0.0069) 16,962 and 89. C1 met (landmark 0.752). XGBoost C2 0.749 (0.692–0.798), just below; logistic regression 0.764 and ensemble 0.763 meet it. C4 **not assessable** (G3 requires 71 development events) | **Exploratory signal of prediction**; rule added after the second classification; the primary learner was not changed after seeing results |

## 6. M3. Fetal death (MIMIC-IV)

One outcome with two prediction points: admission + 6 h (stopped at S6) and, through return R3, antenatal (stopped at G3). Repository: [PerinatalMortality_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalMortality_MIMIC-IV) (notebooks 01 to 03, admission + 6 h; notebooks 04 to 06, return R3).

### Prediction point at admission + 6 h

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | MIMIC-IV v3.1 | — |
| S2 Universe and population | Yes | 19,440 admissions (deliveries or stillbirth code) | D3 |
| S3 Outcome definition | Yes | Verified code catalogue; 129 strict events, 142 with partial | D5: strict code as main label |
| S4 Count audit | Yes | Groups A to E and G; timing audit (notebook 03, Q-G6): **73 of 129 deaths (56.6%) already present at the prediction point** | — |
| G1 Definition | Passes | Label counts reproduced | — |
| S5 Unit of analysis | In progress | Decided (delivery hospitalization; patient as group); script written, not run | D4 |
| S6 Prediction point | Yes | At 6 h all 129 events are kept; at 24 h 13.2% are lost | D6: admission + 6 h |
| G2, S7, G3, S8, S9, G4, S10 | Not performed | — | The audit anticipated that the model would fail C1 |
| **Decision** | — | More than half of the events were already present at the prediction point | **Anticipated return R3** (another prediction point): M3 · R3. The 6-hour model was not fitted |
| S11 Reporting | Pending | — | The audit and the reason for the return are reported |

### Return R3: antenatal prediction point

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | MIMIC-IV v3.1 | — |
| S2 Universe and population | Yes | 25,736 obstetric admissions; population: 19,440 admissions of 13,735 patients | — |
| S3 Outcome definition | Yes | 129 strict events (0.66%), 142 with partial | Strict code as main label |
| S4 Count audit | Yes | With a 7-day buffer, 99 of 129 events (76.7%) and 88.4% of non-events have prior information | The model is feasible in available data |
| G1 Definition | Passes | Population and label counts reproduced | — |
| S5 Unit of analysis | Yes | — | Delivery hospitalization; patient as group |
| S6 Prediction point | Yes | — | Admission; only information from 7 days before or earlier; current pregnancy between 280 and 7 days before; 1- and 30-day buffers as sensitivity |
| G2 Leakage | Passes | Nothing from the current admission (not even admission type or location); partitions grouped by patient | — |
| S7 Predictors | Yes | 27 parameters; verified table (19,440 rows per buffer, no duplicates); missing: blood pressure 38.7%, hemoglobin 41.2%, BMI 57.3% | Multiple imputation; reduced set of 17 fixed beforehand |
| **G3 Sample size** | **Fails** | Cross-validated Cox-Snell R²: −0.00221 (−2.9% of the maximum 0.0767); minimum for 27 parameters: 0.0124; for 17: 0.0078 | **Feasibility analysis**: pre-admission information does not separate the events |
| Sample-size reduction | Not performed | — | Applies only to a model that meets G4; with a negative R² it would not pass with 17 parameters either |
| S8 to S10, G4 | Not performed | — | — |
| S11 Reporting | Pending | — | Reported as feasibility |

## 7. M4. Neonatal mortality (NCHS)

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | NCHS public-use files 2017/2016 to 2024/2023; data use agreement; cells under 10 suppressed | Data kept outside the repository |
| S2 Universe and population | Yes | 8 birth cohorts (2016–2023): 3.59 to 3.94 million live births of 22 weeks or more per cohort; 100% of infant deaths joined to their birth | US-resident mothers, 22 to 47 weeks |
| S3 Outcome definition | Yes | 9,574 to 11,497 deaths at 0 to 27 days per cohort (5,164 to 5,850 at 28 weeks or more) | Main label 0–27 days; sensitivity: 0–6 days, 48-h landmark (originally 24 h; M4-D1), 28 weeks or more |
| S4 Count audit | Yes | 29,829,867 live births and 82,559 neonatal deaths; 46.6% of the deaths in the 0.59% of births at 22–27 weeks. Day-1 deaths change coding between 2016 and 2019; marital status is not reported for ~12% from 2017; all other candidates ≤3% unknown except the interpregnancy interval (11.6%) | Decisions M4-D1 (48-h landmark), M4-D2 (no marital status), M4-D3 (imputation without missingness indicators; complete records as sensitivity) |
| G1 Definition | Passes | Adapter counts reproduced in the 8 cohorts | — |
| S5 Unit of analysis | Yes | No mother identifier | Birth; the group is the birth itself |
| S6 Prediction point | Yes | — | Birth + 5 min; everything later excluded (NICU, ventilation, 10-minute Apgar) |
| G2 Leakage | Passes | Nothing after the prediction point; no missingness indicators; no marital status. The number of unknowns alone gives AUROC 0.601 (0.571 at 28 weeks or more). XGBoost with its own missing handling adds 0.002 | Complete-record sensitivity |
| S7 Predictors | Yes | 71 parameters; 0% to 3% missing except the interpregnancy interval (12.7%) | Contract table; reference model: gestational age + birthweight |
| G3 Sample size | Passes | Development 2016–2021: 22,578,517 births, 62,713 events (883 per parameter). Planning (assumed R² 0.0056): 113,174 births and 315 events required. Reassessment: R² 0.0201 (53% of maximum); 37,427 births and 104 events required | — |
| S8 Preliminary run | Yes | AUROC on the comparison part: LR 0.958, RF 0.960, ET 0.962, XGBoost 0.964 (0.962–0.966); ensemble 0.963 (−0.0007, not adopted) | Primary learner: XGBoost (selection rule) |
| S8 Formal evaluation | Yes | Recalibrated 3 × 5 cross-validation: XGBoost 0.964 (0.963–0.965), AUPRC 0.492, slope 0.98, O/E 0.99; 91.5% of the deaths in the top 10%. LR 0.958. Reference (gestational age + birthweight) 0.907; difference +0.057 (0.056–0.058). Temporal 2022–2023: 0.961 (0.959–0.962), slope 1.01 (0.97–1.03), O/E 1.05 | — |
| S9 Sensitivity | Yes | 28 weeks or more 0.936 (reference 0.829); 48 h and 28 weeks or more 0.893; early neonatal 0.981; complete records 0.959 (refitted 0.960); cohorts 2020–2021: 0.964, O/E 0.94 | — |
| G4 Classification | Yes | C1: 55% of the deaths on days 0 and 1; at 48 h AUROC 0.932 (drop 0.032; 0.031–0.033) → met; AUPRC gain kept 26% (25–27%), reported. C2 met. C3 not applicable. C4 met | **Predictor** |
| S10 From classification to action | Yes | Temporal test 2022–2023. Recommended cut, risk ≥ 1%: 19 flagged per 1,000 births, sensitivity 0.82, PPV 0.12, 8.5 alerts per death found. Subgroups (race and origin, payment, plurality, cohort): AUROC 0.942–0.972, no discrimination flag; one calibration flag (unknown race, O/E 1.45). Condition at birth (Apgar, weight, gestational age) explains 68% of the risk and prenatal or delivery care 13.5% | Use: prioritizing the highest-risk newborns from birth ([`e10_action_neonatal.md`](https://github.com/Tesis-Doctoral/NeonatalMortality_NCHS/blob/main/docs/e10_action_neonatal.md)) |
| S11 | Pending | — | — |

## 8. M5. Perinatal mortality (NCHS)

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | NCHS public-use files: linked births and infant deaths (cohorts 2016–2023) and fetal deaths (2016–2023) | Data kept outside the repository |
| S2 Universe and population | Yes | 29,963,276 products of 22 to 47 weeks of US-resident mothers (29,829,867 live births and 133,409 fetal deaths) | One row per product |
| S3 Outcome definition | Yes | 194,174 events (6.5 per 1,000): 133,409 fetal deaths and 60,765 deaths at 0 to 6 days | Fetal death at 22 weeks or more or death at 0 to 6 days |
| S4 Count audit | Yes | Unknowns are 5 to 18 times more frequent on the report of fetal death; several variables depend on the length of pregnancy | Decisions D3 to D5 |
| G1 Definition | Passes | Adapter counts reproduced in the 8 years | — |
| S5 Unit of analysis | Yes | No mother identifier | Product; the group is the unit itself |
| S6 Prediction point | Yes | — | D2 = option A, antenatal. If classified as *insufficient signal*, option B (at admission) is declared as return R3 |
| G2 Leakage | Passes | No delivery variables or variables excluded by D4; no missingness indicators. The number of unknowns alone gives AUROC 0.639 | Mandatory sensitivity on complete records only |
| S7 Predictors | Yes | 24 parameters; missingness between 0% and 4.5% | Contract table |
| G3 Sample size | Passes | Observed Cox-Snell R² 0.0058 (7.6% of the maximum); 37,147 products and 245 events required; 22,681,046 and 149,057 available | Primary learner: best base learner of a preliminary run |
| S8 Modeling | Yes | Preliminary run with a random search of 20 combinations per learner: XGBoost 0.739, random forest 0.737, extra trees 0.732, logistic regression 0.722; ensemble not adopted. Formal evaluation (5 × 3 folds, recalibrated): XGBoost AUROC 0.739 (0.737–0.740), AUPRC 0.058, slope 1.03, 41.2% of events in the top 10% | Primary learner: XGBoost |
| S9 Sensitivity | Yes | Complete records only 0.689; 28 weeks or more 0.712; fetal deaths only 0.753; antepartum 0.733; early neonatal 0.708. Leakage diagnostic: XGBoost with its own missing handling 0.768 | — |
| G4 C1 / C2 / C3 / C4 | — | C1 by design; C2 not met (0.739 < 0.75); C3 not applicable; C4 met (temporal AUROC 0.735, slope 0.98 within 0.90–1.10, observed/expected 0.90) | — |
| **G4 Classification** | **Insufficient signal** | AUROC below 0.75 | Return R4 |
| R4 (detail in the thesis appendix) 28 weeks or more, complete records, 28 finer predictors (adds gestational diabetes, gestational hypertension, eclampsia, smoking in pregnancy) | Yes | 20,323,696 products and 56,514 events (63% of events vs 89% of products have a complete record); G2 and G3 pass (R² 0.00116); XGBoost 0.676 (0.674–0.679), LR 0.660; temporal 0.677, slope 0.98; 31% of events in the top 10% (3.1×). Sensitivities: all records with median imputation 0.715; fetal deaths 0.690; early neonatal 0.647 | **Insufficient signal** (C2 and C4 not met: AUROC < 0.75 and temporal AUROC < 0.70). The new variables add little (≥28 weeks: 0.712 in version 1 vs 0.715); the drop comes from leaving out the 22–27 week products and the records with unknowns |
| Version 2: version 1 + gestational diabetes, gestational hypertension, eclampsia and smoking during pregnancy (decision of the modeling team, D4 revised) | Yes | 28 parameters; G2 and G3 pass (R² 0.00592). XGBoost 0.742 (0.741–0.743), LR 0.724; stack −0.0006, not adopted; temporal 0.738, slope 0.98, observed/expected 0.90; 41.7% of events in the top 10% (4.2×). Sensitivities: complete records 0.693; 28 weeks or more 0.714; fetal deaths 0.756; early neonatal 0.711. Adjusted odds ratios, all products vs 28 weeks or more: gestational diabetes 0.66 vs 0.96; gestational hypertension 0.85 vs 0.98; eclampsia 1.30 vs 1.29; smoking 1.42 vs 1.42 | **Insufficient signal**: C4 met, C2 not met (0.742 < 0.75). Gain of 0.003 over version 1. The apparent protective effect of gestational diabetes and hypertension comes from the 22–27 week products and disappears at 28 weeks or more |
| Version 2b: version 2 + maternal height, cigarettes in the first trimester, fertility drugs, assisted reproduction, sex of the product and month of the last menstrual period (completes the second return) | Yes | 35 parameters; XGBoost 0.7455 (0.744–0.747); temporal 0.742, slope 0.98; 42.4% of the events in the top 10%. Sensitivities: + father's age 0.748; fetal deaths only 0.760; early neonatal 0.713; complete records 0.689 | **Insufficient signal** (0.0045 below the threshold) |
| S10 From classification to action | Yes | No use for individual decisions (best sensitivity-specificity pair 0.66). Finding: 47% of fetal death reports have an unknown antenatal item, against 14% of birth certificates | Recommendations: improve the fetal death report; use the risk factors for population planning; clinical antenatal data would be needed to predict ([`e10_action_perinatal.md`](https://github.com/Tesis-Doctoral/PerinatalMortality_NCHS/blob/main/docs/e10_action_perinatal.md)) |
| S11 Reporting | Pending | — | — |
| G2 check of the imputation (notebook 10) | Yes | Same folds: median 0.7455; random draw 0.7234 (difference −0.022; −0.023 to −0.022); median with indicators 0.770; native 0.770. In complete records all four give 0.689–0.691: the difference lies only in records with unknowns. Median-imputed data reveal which record had an unknown with AUROC 0.913. Best sensitivity-specificity pair (clean imputation): 0.661 at the balanced point; no threshold gives both ≥ 0.75 | **Median imputation carried form-type signal**: the clean AUROC of version 2b is 0.723. The rule fixed before running sets random-draw imputation from now on. **Model closed** (decision of 2026-11-24): version 3 is not built, because the antenatal variables shared by both files that remain cannot close a gap of 0.027 |

## 9. M6. Fetal death at 22 weeks or more (NCHS)

Verification model of M5: it tests whether the AUROC of 0.760 for fetal deaths in M5 came from the form-type signal (unknowns are far more frequent on the fetal death report). Repository: [FetalDeath_NCHS](https://github.com/Tesis-Doctoral/FetalDeath_NCHS).

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 to S4, G1 | Yes | Population of M5 (22 weeks or more): 133,409 fetal deaths; complete records: 97–98% of live births and 76–81% of fetal deaths | Decisions M6-D1 to M6-D4: only antenatal variables with 10% or fewer unknowns on the fetal report (22 parameters); no conditions that depend on the length of pregnancy; main analysis on complete records |
| S6 Prediction point | Yes | — | Antenatal |
| G2 Leakage | Passes | The number of unknowns alone gives AUROC 0.591 in all records and 0.5 in complete records | **Explicit test of the form signal** |
| G3 Sample size | Passes | 22,127,472 products and 81,454 development events (3,702 per parameter); R² 0.00235; 84,109 products required | — |
| S8 Modeling | Yes | Preliminary run: RF 0.681, XGBoost 0.680, ET 0.680, LR 0.671; primary XGBoost by the rule. Formal evaluation: 0.680 (0.678–0.682), slope 0.97; 32.8% of the events in the top 10% (3.3×). Temporal 0.673, slope 0.98 | Primary learner: XGBoost |
| S9 Sensitivity | Yes | + gestational diabetes and hypertension 0.682; 35 variables on complete records 0.697; all records imputed 0.683 (includes the form signal); antepartum 0.672 | — |
| G4 Classification | Yes | C1 by design; C2 not met (0.680); C3 not applicable; C4 not met (temporal 0.673) | **Insufficient signal**. With the leakage controlled, antenatal fetal death does not exceed 0.70: the 0.760 of M5 came largely from the form type |
| S10, S11 | Done | No individual use (top 10%: sensitivity 0.33, PPV 0.012); reported as the verification of gate G2 of M5 ([`e10_e11_fetal_nchs.md`](https://github.com/Tesis-Doctoral/FetalDeath_NCHS/blob/main/docs/e10_e11_fetal_nchs.md)) | TRIPOD+AI checklist pending |

## 10. Equivalences with the former numbering

The numbering was revised on 2026-11-24 so that the thesis catalogue has no gaps. Repository names do not change. Aggregate outputs generated before that date (`data/outputs_*.txt`) keep the former numbering.

| Current | Former | Model |
|---|---|---|
| M1 | M1 | Maternal death (MIMIC-IV) |
| M2 | M2 | Severe maternal morbidity (MIMIC-IV) |
| M3 | M3 | Fetal death at admission + 6 h (MIMIC-IV) |
| M3 · R3 | M4 | Antenatal fetal death (MIMIC-IV), return R3 of M3 |
| M4 | M5 | Neonatal mortality (NCHS); decisions M5-D1 to M5-D3 → M4-D1 to M4-D3 |
| M5 | M6 | Perinatal mortality (NCHS) |
| M6 | M7 | Fetal death at 22 weeks or more (NCHS); decisions M7-D1 to M7-D4 → M6-D1 to M6-D4 |

*Confidential: internal use of the research project.*
