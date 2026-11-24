# Etapa E10 — de la clasificación a la acción: morbilidad materna grave, MIMIC-IV (M2)

Versión en inglés: [`e10_action_m2_smm.md`](e10_action_m2_smm.md). Este documento usa solo las salidas agregadas ya publicadas en el repositorio del artículo ([MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus): `data/outputs_main.txt`, `data/outputs_race_subgroups.txt`, `data/outputs_escalation.txt`, `data/outputs_g4_fitted_models.txt`); no se corrió ningún análisis nuevo ni se modificó ese repositorio. Cifras de la validación cruzada repetida y agrupada por paciente: 19.383 partos, 445 con SMM (2,3%).

## 1. Clase y uso

El modelo es **identificador temprano**: a las 24 horas del ingreso reconoce bien a las pacientes con SMM (AUROC 0,791; 0,767–0,816), pero buena parte de lo que reconoce ya estaba ocurriendo (en la población *landmark* el AUROC baja a 0,719). Por eso su uso no es la prevención sino **la vigilancia y la priorización de la revisión clínica**:

- una lista diaria, a las 24 horas del ingreso, de las pacientes con mayor riesgo, para que el equipo obstétrico revise primero su historia, sus exámenes y su plan;
- apoyo a la auditoría de casos de morbilidad materna grave (identificar a tiempo los casos que deben notificarse y analizarse).

No debe usarse para decidir intervenciones preventivas: el modelo no anticipa la morbilidad lo suficiente como para eso.

## 2. Punto de operación y carga de trabajo

| Punto de operación | Revisiones por 100 partos | Sensibilidad | VPP | Revisiones por caso de SMM encontrado |
|---|---|---|---|---|
| **5% de mayor riesgo** | **5** | **0,357 (0,320–0,398)** | **0,164** | **6,1** |
| 10% de mayor riesgo | 10 | 0,514 (0,472–0,557) | 0,118 | 8,5 |

**Recomendación: el 5% de mayor riesgo** (el punto de operación del artículo). Revisar 5 de cada 100 partos encuentra uno de cada tres casos de SMM con unas 6 revisiones por caso. Si la entidad tiene más capacidad, el 10% de mayor riesgo encuentra la mitad de los casos con unas 8 o 9 revisiones por caso. El beneficio neto supera al de revisar a todas desde un umbral de riesgo del 1% (0,0142 frente a 0,0131) y la diferencia crece con el umbral (2%: 0,0114 frente a 0,0030).

**Dónde aporta:** casi toda su señal está en las pacientes sin ingreso a cuidado intensivo en las primeras 24 horas (AUROC 0,749). En las que ya ingresaron a cuidado intensivo (115 partos, 90 con SMM), el modelo apenas discrimina (0,610): esas pacientes ya están identificadas.

**Objetivo original de la tesis** (sensibilidad y especificidad mayores de 0,75): con un AUROC de 0,79 el mejor par alcanzable ronda 0,72 (aproximación binormal); no se cumple, aunque queda cerca.

## 3. Equidad: desempeño por raza y etnia (XGBoost)

| Grupo | Partos | Casos | AUROC | Observados/esperados |
|---|---|---|---|---|
| Blanca | 8.694 | 179 | 0,800 (0,761–0,834) | 0,95 (0,83–1,08) |
| Negra | 4.444 | 115 | 0,770 (0,717–0,826) | 0,98 (0,84–1,13) |
| Hispana o latina | 1.913 | 30 | 0,744 (0,657–0,827) | 0,82 (0,54–1,15) |
| Asiática | 1.792 | 50 | 0,796 (0,722–0,855) | 1,24 (0,94–1,56) |
| Otra | 1.616 | 37 | 0,822 (0,755–0,888) | 1,02 (0,74–1,33) |
| Desconocida o no declarada | 924 | 34 | 0,799 (0,711–0,885) | 1,32 (0,91–1,79) |

- La discriminación es parecida entre grupos; los intervalos se solapan. El grupo hispano tiene el AUROC más bajo y una tendencia a la sobreestimación del riesgo, con pocos casos (30).
- Quitar la raza del modelo no cambia la discriminación global (diferencia 0,000) pero empeora la calibración en los grupos hispano (O/E 0,72) y asiático (1,39). Mantenerla mejora la calibración por grupo.
- En la población *landmark* la discriminación baja más en las pacientes negras (0,689) que en las blancas (0,741): el modelo es menos útil para anticipar la SMM en ese grupo. Es una precaución explícita para la implementación.

## 4. El camino hacia un predictor: escalamiento de la atención (retorno R2)

El escalamiento de la atención después de las 24 horas (ingreso a cuidado intensivo, ventilación mecánica, un procedimiento indicador de SMM o la muerte) sí cumplió los criterios de un predictor (AUROC 0,763; *landmark* 0,761; temporal 0,714), pero con 101 eventos no pasa la puerta P3. En el 10% de mayor riesgo concentra el 46,5% de los escalamientos, con unas 41 revisiones por escalamiento encontrado. Es la línea a validar con datos multicéntricos: si se confirma con más eventos, permitiría anticipar el deterioro y no solo reconocer la morbilidad.

## 5. Condiciones para implementarlo

1. **Recalibración obligatoria.** En los años posteriores el modelo subestimó el riesgo (pendiente 0,83; O/E 1,34). Antes de usarlo en otra institución o época hay que recalibrarlo con datos locales y vigilar la calibración cada año.
2. **Validación externa.** Se desarrolló en un solo hospital de alta complejidad (Beth Israel Deaconess, Boston). Debe validarse en hospitales con otra población y otra organización de la atención, por ejemplo en Colombia.
3. **Datos a las 24 horas.** Requiere la edad, las variables de registro del ingreso, la hemoglobina y la creatinina disponibles a las 24 horas. Si el laboratorio no se pidió, el modelo pierde parte de su información.
4. **Uso como lista de revisión**, no como alarma automática ni como criterio de tratamiento.

## 6. Limitaciones

- La SMM es una clasificación administrativa con códigos de diagnóstico de toda la hospitalización; su momento solo se pudo fechar en una parte de los casos.
- Una sola institución, datos de 2008 a 2022.
- El sesgo de detección (criterio C3): la falla renal se predice sobre todo en las pacientes a las que se les midió creatinina.
