# Models that apply the framework

Catalogue of every model designed in the thesis, including those that did not pass a gate, with the link to its repository and the status of each stage and gate. Updated on 2026-11-21. Spanish version: [`models_es.md`](models_es.md). The framework is described in [`README.md`](README.md).

**Status conventions.** **Yes**: the task was done. **In progress**: started, not finished. **Pending**: not done yet, but will be. **Not performed**: will not be done because the model stopped at an earlier gate. **Not applicable**: the criterion does not apply to that outcome. Gates show whether the model **passes** or **fails**, with the indicators obtained and the decision taken. AUROC with its 95% CI in parentheses.

## 1. Summary

| Id | Model | Outcome | Source | Prediction point | Reached | Result | Repository |
|---|---|---|---|---|---|---|---|
| M1 | Maternal death | Death in the delivery hospitalization | MIMIC-IV v3.1 | Admission + 24 h | G3 | **Fails G3** (3 events): feasibility; return R1 to M2 | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M2 | Severe maternal morbidity (SMM) | 20 CDC indicators | MIMIC-IV v3.1 | Admission + 24 h | S11 | **Early identifier**; return R2 (escalation): **exploratory signal of prediction** | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M3 | Fetal death at admission | Fetal death (maternal ICD codes) | MIMIC-IV v3.1 | Admission + 6 h | S6 | Stopped before modeling: 73 of 129 deaths already present at the prediction point; return R3 to M4 | [PerinatalMortality_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalMortality_MIMIC-IV) |
| M4 | Antenatal fetal death | Fetal death (maternal ICD codes) | MIMIC-IV v3.1 | Admission (information from 7 days before or earlier) | G3 | **Fails G3** (negative Cox-Snell R²): feasibility | [PerinatalAntenatal_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalAntenatal_MIMIC-IV) |
| M5 | Neonatal mortality | Death at 0 to 27 days | NCHS (CDC), linked births and infant deaths, cohorts 2016–2023 | Birth + 5 min | S3 | In progress: data adapter ready; count audit next | [NeonatalMortality_NCHS](https://github.com/Tesis-Doctoral/NeonatalMortality_NCHS) |
| M6 | Perinatal mortality | Fetal death at 22 weeks or more or neonatal death at 0 to 6 days | NCHS (CDC) | Antenatal (option A) | G3 | In progress: G1, G2 and G3 pass (194,174 events); modeling next | [PerinatalMortality_NCHS](https://github.com/Tesis-Doctoral/PerinatalMortality_NCHS) |

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
| M4 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |  |  |  |
| M5 | ✅ | ✅ | ✅ |  |  | ✅ | ✅ |  |  |  |  |  |  |  |  |
| M6 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |  |  |  |  |  |

**Legend**

- **Stages (S):** ✅ the task was done; blank, not done (pending, in progress, or will not be done because the model stopped earlier).
- **Gates (G):** ✅ passes; ❌ fails.
- **🟡 at gate G4:** the model was classified as an **early identifier** (it did not reach predictor).
- **M2 · R2:** the ✅ at G4 means the classification criteria were met although gate G3 was not passed.
- **M2 · R2 reduced:** the ❌ at G4 means the primary model fell just below the C2 and C4 thresholds (exploratory signal).
- **Return rows (R4, R2):** mark only the stages that were redone; the others are inherited from the main model.
- Details and indicators for each cell are in the following sections.

## 3. How they relate

```
Maternal slot       M1 Maternal death ──R1──► M2 SMM ──R4──► extended laboratory tests
                                                   └─R2──► escalation of care ──► sample-size reduction
Perinatal slot      M3 Fetal death at admission + 6 h ──R3──► M4 Antenatal fetal death
                    M6 Perinatal mortality (NCHS)
Neonatal slot       M5 Neonatal mortality (NCHS)
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
| G3 Sample size | Passes | Observed Cox-Snell R² 0.0311 (maximum 0.196); 9,112 admissions and 209 events required; 19,383 and 445 available; supports up to 68 parameters | Primary learner: XGBoost |
| S8 Modeling | Yes | XGBoost: AUROC 0.791 (0.767–0.816), well calibrated | — |
| S9 Sensitivity | Yes | Definitions, populations, laboratory tests, ICU variables, resampling | — |
| G4 C1 Events present | Not met | Landmark AUROC 0.723 (0.690–0.753): falls 0.068; keeps 38% of the AUPRC gain | — |
| G4 C2 Outcome with time | Borderline | Documented incident 0.745 (0.704–0.788); strict incident 0.798 (0.748–0.850) | — |
| G4 C3 Detection bias | Not met | Renal failure: AUROC 0.850 overall, 0.628 among tested patients | — |
| G4 C4 Stability | Not met | Temporal AUROC 0.747 (0.707–0.783); calibration slope 0.83 (0.71–0.97); observed/expected 1.34 | — |
| **G4 Classification** | **Early identifier** | Fails C1, C3 and C4 | Returns R4 and R2 |
| S10 Action | Yes | Flagging the top 5%: about 970 alerts, positive predictive value 16.4%, about 6 reviews per case | Surveillance and prioritization, not prevention |
| S11 Reporting | Yes | Manuscript and TRIPOD+AI checklist | — |

### Returns and reduction

| Step | Done | Indicators | Decision |
|---|---|---|---|
| R4 Extended laboratory tests (10 tests with missingness indicators) | Yes | AUROC 0.821 (0.799–0.844); landmark 0.737; without missingness indicators 0.726; C3: 0.861 falls to 0.643; C4: temporal landmark AUROC 0.697, slope 0.73 | **Remains an early identifier**: the gain came from which tests were ordered |
| R2 Escalation of care after 24 h | Yes | 101 events in 19,268 deliveries (0.52%); AUROC 0.763 (0.709–0.808); 46.5% of events in the top 10%; C1: 0.761; C2 met; C3 not applicable; C4: temporal AUROC 0.714 (0.629–0.795), slope 0.98 (0.60–1.31) | Meets G4 but **fails G3**: R² 0.0077; 37,098 admissions required; supports 16 of 32 parameters |
| Sample-size reduction (13 parameters) | Yes | G3 **passes**: R² 0.0069, 16,962 admissions and 89 events required. C1 met (0.754 vs 0.749). XGBoost: C2 0.749 (0.692–0.798), C4 0.699 (0.620–0.778), just below the thresholds; logistic regression meets them (0.764; 0.712) | **Exploratory signal of prediction (borderline)**; the primary learner was not changed after seeing results |

## 6. M3. Fetal death at admission + 6 h (MIMIC-IV)

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | MIMIC-IV v3.1 | — |
| S2 Universe and population | Yes | 19,440 admissions (deliveries or stillbirth code) | D3 |
| S3 Outcome definition | Yes | Verified code catalogue; 129 strict events, 142 with partial | D5: strict code as main label |
| S4 Count audit | Yes | Groups A to E and G; T45: **73 of 129 deaths (56.6%) already present at the prediction point** | — |
| G1 Definition | Passes | Label counts reproduced | — |
| S5 Unit of analysis | In progress | Decided (delivery hospitalization; patient as group); script written, not run | D4 |
| S6 Prediction point | Yes | At 6 h all 129 events are kept; at 24 h 13.2% are lost | D6: admission + 6 h |
| G2, S7, G3, S8, S9, G4, S10 | Not performed | — | The audit anticipated that the model would fail C1 |
| **Decision** | — | More than half of the events were already present at the prediction point | **Anticipated return R3** (another prediction point): M4. The 6-hour model was not fitted |
| S11 Reporting | Pending | — | The audit and the reason for the return are reported |

## 7. M4. Antenatal fetal death (MIMIC-IV)

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
| **G3 Sample size** | **Fails** | Cross-validated Cox-Snell R²: −0.00054 (−0.7% of the maximum 0.0767); minimum for 27 parameters: 0.0124; for 17: 0.0078 | **Feasibility analysis**: pre-admission information does not separate the events |
| Sample-size reduction | Not performed | — | Applies only to a model that meets G4; with a negative R² it would not pass with 17 parameters either |
| S8 to S10, G4 | Not performed | — | — |
| S11 Reporting | Pending | — | Reported as feasibility |

## 8. M5. Neonatal mortality (NCHS)

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | NCHS public-use files 2017/2016 to 2024/2023; data use agreement; cells under 10 suppressed | Data kept outside the repository |
| S2 Universe and population | Yes | 8 birth cohorts (2016–2023): 3.59 to 3.94 million live births of 22 weeks or more per cohort; 100% of infant deaths joined to their birth | US-resident mothers, 22 to 47 weeks |
| S3 Outcome definition | Yes | 9,574 to 11,497 deaths at 0 to 27 days per cohort (5,164 to 5,850 at 28 weeks or more) | Main label 0–27 days; sensitivity: 0–6 days, 24-h landmark, 28 weeks or more |
| S4 Count audit | Pending | — | Next step |
| G1 Definition | Pending | Reference counts written in `data/outputs_01_build_cohorts.txt` | — |
| S5 Unit of analysis | Yes | No mother identifier | Birth; the group is the birth itself |
| S6 Prediction point | Yes | — | Birth + 5 min; everything later excluded (NICU, ventilation, 10-minute Apgar) |
| G2, S7, G3, S8, S9, G4, S10, S11 | Pending | — | — |

## 9. M6. Perinatal mortality (NCHS)

| Stage or gate | Done | Indicators | Decision |
|---|---|---|---|
| S1 Governance and configuration | Yes | NCHS public-use files: linked births and infant deaths (cohorts 2016–2023) and fetal deaths (2016–2023) | Data kept outside the repository |
| S2 Universe and population | Yes | 29,963,276 products of 22 to 47 weeks of US-resident mothers (29,829,867 live births and 133,409 fetal deaths) | One row per product |
| S3 Outcome definition | Yes | 194,174 events (6.5 per 1,000): 133,409 fetal deaths and 60,765 deaths at 0 to 6 days | Fetal death at 22 weeks or more or death at 0 to 6 days |
| S4 Count audit | Yes | Unknowns are 5 to 18 times more frequent on the report of fetal death; several variables depend on the length of pregnancy | Decisions D3 to D5 |
| G1 Definition | Passes | Adapter counts reproduced in the 8 years | — |
| S5 Unit of analysis | Yes | No mother identifier | Product; the group is the unit itself |
| S6 Prediction point | Yes | — | D2 = option A, antenatal. If classified as *neither*, option B (at admission) is declared as return R3 |
| G2 Leakage | Passes | No delivery variables or variables excluded by D4; no missingness indicators. The number of unknowns alone gives AUROC 0.639 | Mandatory sensitivity on complete records only |
| S7 Predictors | Yes | 24 parameters; missingness between 0% and 4.5% | Contract table |
| G3 Sample size | Passes | Observed Cox-Snell R² 0.0058 (7.6% of the maximum); 37,147 products and 245 events required; 22,681,046 and 149,057 available | Primary learner: best base learner of a preliminary run |
| S8, S9, G4, S10, S11 | Pending | — | — |

*Confidential: internal use of the research project.*
