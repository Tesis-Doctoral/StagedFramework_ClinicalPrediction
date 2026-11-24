# Modelos que aplican el framework

Catálogo de todos los modelos ideados en la tesis, incluidos los que no pasaron alguna puerta, con el enlace a su repositorio y el estado de cada etapa y puerta. Actualizado el 24/11/2026; numeración de los modelos revisada ese día (equivalencias en la sección 10). Versión en inglés: [`models.md`](models.md). El framework se describe en [`README_es.md`](README_es.md).

**Convenciones de estado.** **Sí**: la tarea se realizó. **En curso**: empezada, sin terminar. **Pendiente**: aún no se ha hecho, pero se hará. **No se realiza**: no se hará porque el modelo se detuvo en una puerta anterior. **No aplica**: el criterio no corresponde a ese desenlace. En las puertas se indica si **pasa** o **no pasa**, con los indicadores obtenidos y la decisión tomada. AUROC con su IC 95% entre paréntesis.

**Clases de resultado.** Tras la puerta P4: **predictor**, **identificador temprano** y **señal insuficiente** (*neither* en la figura del framework y en el artículo de Cureus). Antes de P4: **exploratorio** (no pasa P3 y deriva en otro modelo por R1) y **factibilidad** (no pasa P3 y no queda peldaño ni retorno útil). Un **modelo derivado** nace de un retorno R1 (otro peldaño de la escalera de desenlaces) y tiene sus propios dos retornos; un **modelo de verificación** se construye para comprobar un hallazgo de otro modelo.

## 1. Resumen

| Id | Modelo | Desenlace | Fuente | Punto de predicción | Origen | Hasta dónde llegó | Resultado | Repositorio |
|---|---|---|---|---|---|---|---|---|
| M1 | Muerte materna | Muerte en la hospitalización de parto | MIMIC-IV v3.1 | Ingreso + 24 h | — | P3 | **Exploratorio**: no pasa P3 (3 eventos); deriva en M2 por R1 | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M2 | Morbilidad materna grave (SMM) | 20 indicadores de los CDC | MIMIC-IV v3.1 | Ingreso + 24 h | Derivado de M1 (R1) | E11 | **Identificador temprano** (AUROC 0,791); retornos R4 (identificador temprano) y R2 (escalamiento: señal exploratoria de predicción). Artículo de Cureus | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M3 | Muerte fetal | Muerte fetal (códigos CIE de la madre) | MIMIC-IV v3.1 | Ingreso + 6 h; retorno R3: prenatal (información de 7 días antes o más) | — | P3 | **Factibilidad**: al ingreso + 6 h se detuvo en E6 (73 de 129 muertes ya presentes); con R3 (prenatal) no pasa P3 (R² de Cox-Snell negativo) | [PerinatalMortality_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalMortality_MIMIC-IV), [PerinatalAntenatal_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalAntenatal_MIMIC-IV) |
| M4 | Mortalidad neonatal | Muerte de 0 a 27 días | NCHS (CDC), nacimientos vinculados con defunciones, cohortes 2016–2023 | Nacimiento + 5 min | — | P4 | **Predictor** con el framework 1.3 (XGBoost, AUROC 0,964; a las 48 h 0,932, caída 0,032; temporal 0,961, pendiente 1,01). Con el framework 1.2 era identificador temprano | [NeonatalMortality_NCHS](https://github.com/Tesis-Doctoral/NeonatalMortality_NCHS) |
| M5 | Mortalidad perinatal | Muerte fetal de 22 semanas o más o muerte neonatal de 0 a 6 días | NCHS (CDC) | Prenatal (opción A) | — | P4 | **Señal insuficiente, cerrado el 24/11/2026** (XGBoost, AUROC limpio 0,723; 0,7455 con la señal del formulario que traía la imputación por la mediana; umbral 0,75; cumple C4). Retornos usados: R4 (28 semanas o más, 0,676; detalle en anexo) y versión 2. Verificación de P2 (24/11/2026): la imputación por la mediana llevaba señal del tipo de formulario; con imputación por sorteo aleatorio el AUROC es 0,723 | [PerinatalMortality_NCHS](https://github.com/Tesis-Doctoral/PerinatalMortality_NCHS) |
| M6 | Muerte fetal de 22 semanas o más | Muerte fetal frente a nacido vivo | NCHS (CDC) | Prenatal | Verificación de M5 (P2) | P4 | **Señal insuficiente** (XGBoost, AUROC 0,680; temporal 0,673). Valida la puerta P2: sin la señal del tipo de formulario, la muerte fetal baja de 0,760 a 0,680 | [FetalDeath_NCHS](https://github.com/Tesis-Doctoral/FetalDeath_NCHS) |

Repositorio de este framework: [StagedFramework_ClinicalPrediction](https://github.com/Tesis-Doctoral/StagedFramework_ClinicalPrediction). Todos los repositorios están en la organización [Tesis-Doctoral](https://github.com/Tesis-Doctoral).

## 2. Matriz de etapas y puertas

| Modelo | E1 | E2 | E3 | E4 | **P1** | E5 | E6 | **P2** | E7 | **P3** | E8 | E9 | **P4** | E10 | E11 |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| M1 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |  |  | ✅ |
| M2 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | 🟡 | ✅ | ✅ |
| M2 · R4 |  |  |  |  |  |  |  |  | ✅ |  | ✅ | ✅ | 🟡 |  | ✅ |
| M2 · R2 |  |  | ✅ | ✅ | ✅ |  |  | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ |  | ✅ |
| M2 · R2 reducido |  |  |  |  |  |  |  |  | ✅ | ✅ | ✅ | ✅ | ❌ |  | ✅ |
| M3 | ✅ | ✅ | ✅ | ✅ | ✅ |  | ✅ |  |  |  |  |  |  |  |  |
| M3 · R3 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |  |  |  |
| M4 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |  |  |
| M5 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |
| M6 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |  |  |

**Leyenda**

- **Etapas (E):** ✅ la tarea se realizó; en blanco, no se realizó (pendiente, en curso o no se hará porque el modelo se detuvo antes).
- **Puertas (P):** ✅ pasa; ❌ no pasa.
- **En la puerta P4:** ✅ predictor; 🟡 **identificador temprano**; ❌ **señal insuficiente**.
- **M2 · R2:** la ✅ de P4 indica que se cumplieron C1 y C2 (C4 no evaluable por falta de eventos), aunque la puerta P3 no se pasó.
- **M2 · R2 reducido:** la ❌ de P4 indica que el modelo principal quedó justo bajo el umbral de C2 y que C4 no pudo evaluarse (señal exploratoria).
- **Filas de retornos (R4, R2, R3):** solo marcan las etapas que se rehicieron; las demás se heredan del modelo principal.
- El detalle y los indicadores de cada celda están en las secciones siguientes.

## 3. Cómo se relacionan

```
Ranura materna      M1 Muerte materna ──R1 (derivado)──► M2 SMM ──R4──► laboratorios ampliados
                                                              └─R2──► escalamiento de la atención ──► reducción por tamaño de muestra
Ranura fetal        M3 Muerte fetal (MIMIC-IV): ingreso + 6 h ──R3──► prenatal
Ranura perinatal    M5 Mortalidad perinatal (NCHS) ──R4──► 28 semanas o más (anexo)
                                                └─versión 2──► + cuatro condiciones del embarazo (2b: + seis variables)
                    M5 ──verificación de P2──► M6 Muerte fetal (NCHS)
Ranura neonatal     M4 Mortalidad neonatal (NCHS)
```

## 4. M1. Muerte materna (MIMIC-IV)

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | MIMIC-IV v3.1, descargada el 18/04/2025 | Compartida con M2 |
| E2 Universo y población | Sí | 25.736 admisiones obstétricas; 19.383 partos elegibles a las 24 h | Compartida con M2 |
| E3 Definición del desenlace | Sí | Muerte en la misma admisión: 10 en el universo, 3 entre los partos elegibles | Desenlace ideal (primer peldaño) |
| E4 Auditoría de conteos | Sí | 3 eventos | — |
| P1 Definición | Pasa | Conteos reproducidos | — |
| E5 a E7 | Sí | Compartidas con M2 (32 parámetros candidatos) | — |
| P2 Fuga | Pasa | Compartida con M2 | — |
| **P3 Tamaño de muestra** | **No pasa** | Con 32 parámetros, los criterios de Riley exigían al menos 33 muertes aun con el R² supuesto más optimista; había 3 | **Análisis de factibilidad; retorno R1 al siguiente peldaño: SMM (M2)** |
| E8 a E11, P4 | No se realiza | — | El modelo no se ajusta; se reporta como factibilidad dentro de M2 |

## 5. M2. Morbilidad materna grave a las 24 horas (MIMIC-IV)

### Modelo principal

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | MIMIC-IV v3.1; semilla, versiones y conteos de referencia fijos | — |
| E2 Universo y población | Sí | Universo: 25.736 admisiones (15.084 pacientes); 19.425 partos; 19.383 elegibles a las 24 h (13.710 pacientes) | Hospitalizaciones de parto aún hospitalizadas a las 24 h |
| E3 Definición del desenlace | Sí | 445 eventos (2,30%). Momento: 150 presentes en el punto de predicción (33,7%), 10 indeterminados, 114 incidentes documentados (25,6%), 171 sin fecha (38,4%) | Al menos 1 de 20 indicadores de los CDC; sensibilidad con 2 o más |
| E4 Auditoría de conteos | Sí | Flujo de cohorte, clases temporales, solapamiento muerte–SMM | — |
| P1 Definición | Pasa | Verificación `EXPECTED` en cada notebook | — |
| E5 Unidad de análisis | Sí | 45% de las pacientes con más de una admisión | Hospitalización de parto; paciente como grupo |
| E6 Punto de predicción | Sí | — | Ingreso + 24 h |
| P2 Fuga | Pasa | Grupos de diagnósticos de egreso retirados; particiones agrupadas por paciente | — |
| E7 Predictores | Sí | Edad, 5 variables de registro, hemoglobina, creatinina e indicador de faltante de creatinina; 32 parámetros; regla de cobertura del 10% | — |
| P3 Tamaño de muestra | Pasa | Planeación (R² de Cox-Snell supuesto 0,030, 15% del máximo): se requerían 9.614 admisiones y 221 eventos. Reevaluación (R² observado fuera de muestra 0,0311): 9.112 y 209. Disponibles: 19.383 y 445; 32 parámetros (columnas después de la codificación *one-hot*, sin intercepto); soporta hasta 68 | Modelo principal: XGBoost |
| E8 Modelado | Sí | XGBoost: AUROC 0,791 (0,767–0,816), bien calibrado | — |
| E9 Sensibilidad | Sí | Definiciones, poblaciones, laboratorios, variables de UCI, remuestreo | — |
| P4 C1 Eventos presentes | No cumple | Sobre el modelo ajustado: AUROC *landmark* 0,719 (0,686–0,750); caída 0,072 (0,056–0,088); conserva el 40% (30–51%) de la ganancia de AUPRC | — |
| P4 C2 Desenlace con tiempo | Cumple | Sobre el modelo ajustado: incidentes documentados 0,765 (0,713–0,816); incidentes estrictos 0,784 (0,721–0,843). Reentrenado en cada subconjunto había quedado en el límite (0,745) | — |
| P4 C3 Sesgo de detección | No cumple | Falla renal: AUROC 0,850 (0,822–0,872) en todas, 0,628 (0,584–0,671) entre las examinadas | — |
| P4 C4 Estabilidad | No cumple | AUROC temporal 0,747 (0,707–0,783); pendiente de calibración 0,83 (0,71–0,97); observados/esperados 1,34 (1,15–1,52); el período de desarrollo pasa P3. Banda de tolerancia de la pendiente 0,90–1,10 (sensibilidad): no cumple | — |
| **P4 Clasificación** | **Identificador temprano** | Falla C1, C3 y C4; cumple C2 | Retornos R4 y R2 |
| E10 Acción | Sí | Marcar el 5% de mayor riesgo: unas 970 alertas, valor predictivo positivo 16,4%, unas 6 revisiones por caso | Vigilancia y priorización, no prevención |
| E11 Reporte | Sí | Manuscrito y lista TRIPOD+AI | — |

### Retornos y reducción

| Paso | Realizada | Indicadores | Decisión |
|---|---|---|---|
| R4 Laboratorios ampliados (10 exámenes con indicador de faltante) | Sí | AUROC 0,821 (0,799–0,844); *landmark* 0,740, caída 0,081 (0,066–0,098), conserva el 29% de la ganancia; C2 cumple (0,763; 0,776); C3: 0,861 baja a 0,643; C4: AUROC temporal *landmark* 0,697, pendiente 0,73 (0,56–0,90) | **Sigue como identificador temprano**: la ganancia venía de qué exámenes se pidieron |
| R2 Escalamiento de la atención después de las 24 h | Sí | 101 eventos en 19.268 partos (0,52%); AUROC 0,763 (0,709–0,808); 46,5% de los eventos en el 10% superior; C1 cumple (*landmark* 0,765); C2 cumple; C3 no aplica; C4 **no evaluable**: 46 eventos en el período de desarrollo, P3 exige 155. Componentes: UCI 0,770, ventilación 0,758, indicador por procedimiento 0,702 | **No pasa P3**: planeación 30.120 admisiones y 158 eventos; reevaluación (R² 0,0077) 37.098 y 194; soporta 16 de 32 parámetros |
| Reducción por tamaño de muestra (13 parámetros) | Sí | P3 **pasa**: planeación 12.236 y 64; reevaluación (R² 0,0069) 16.962 y 89. C1 cumple (*landmark* 0,752). XGBoost C2 0,749 (0,692–0,798), justo por debajo; la regresión logística (0,764) y el ensamble (0,763) lo cumplen. C4 **no evaluable** (P3 exige 71 eventos de desarrollo) | **Señal exploratoria de predicción**; regla agregada después de la segunda clasificación; el modelo principal no se cambió después de ver resultados |

## 6. M3. Muerte fetal (MIMIC-IV)

Un solo desenlace con dos puntos de predicción: al ingreso + 6 h (detenido en E6) y, por el retorno R3, prenatal (detenido en P3). Repositorios: [PerinatalMortality_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalMortality_MIMIC-IV) y [PerinatalAntenatal_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalAntenatal_MIMIC-IV).

### Punto de predicción al ingreso + 6 h

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | MIMIC-IV v3.1 | — |
| E2 Universo y población | Sí | 19.440 admisiones (partos o código de mortinato) | D3 |
| E3 Definición del desenlace | Sí | Catálogo verificado de códigos; 129 eventos estrictos, 142 con parciales | D5: código estricto como etiqueta principal |
| E4 Auditoría de conteos | Sí | Grupos A a E y G; T45: **73 de 129 muertes (56,6%) ya presentes en el punto de predicción** | — |
| P1 Definición | Pasa | Conteos de la etiqueta reproducidos | — |
| E5 Unidad de análisis | En curso | Decidida (hospitalización de parto; paciente como grupo); script escrito sin ejecutar | D4 |
| E6 Punto de predicción | Sí | Con 6 h se conservan los 129 eventos; con 24 h se pierde el 13,2% | D6: ingreso + 6 h |
| P2, E7, P3, E8, E9, P4, E10 | No se realiza | — | La auditoría anticipó que el modelo fallaría C1 |
| **Decisión** | — | Más de la mitad de los eventos ya estaba presente al predecir | **Retorno R3 anticipado** (otro punto de predicción): M3 · R3. El modelo a las 6 h no se ajustó |
| E11 Reporte | Pendiente | — | Se reporta la auditoría y la razón del retorno |

### Retorno R3: punto de predicción prenatal

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | MIMIC-IV v3.1 | — |
| E2 Universo y población | Sí | 25.736 admisiones obstétricas; población: 19.440 admisiones de 13.735 pacientes | — |
| E3 Definición del desenlace | Sí | 129 eventos estrictos (0,66%), 142 con parciales | Código estricto como etiqueta principal |
| E4 Auditoría de conteos | Sí | Con margen de 7 días tienen información previa 99 de 129 eventos (76,7%) y el 88,4% de los no eventos | El modelo es factible en datos disponibles |
| P1 Definición | Pasa | Conteos de población y etiqueta reproducidos | — |
| E5 Unidad de análisis | Sí | — | Hospitalización de parto; paciente como grupo |
| E6 Punto de predicción | Sí | — | Ingreso; solo información de 7 días antes o más; embarazo actual entre 280 y 7 días antes; márgenes de 1 y 30 días como sensibilidad |
| P2 Fuga | Pasa | Nada del ingreso actual (ni tipo ni lugar de admisión); particiones agrupadas por paciente | — |
| E7 Predictores | Sí | 27 parámetros; tabla verificada (19.440 filas por margen, sin duplicados); faltan presión 38,7%, hemoglobina 41,2%, IMC 57,3% | Imputación múltiple; conjunto reducido de 17 fijado antes |
| **P3 Tamaño de muestra** | **No pasa** | R² de Cox-Snell con validación cruzada: −0,00054 (−0,7% del máximo 0,0767); mínimo para 27 parámetros: 0,0124; para 17: 0,0078 | **Análisis de factibilidad**: la información previa al ingreso no separa los eventos |
| Reducción por tamaño de muestra | No se realiza | — | Solo aplica a un modelo que cumple P4; con R² negativo tampoco pasaría con 17 parámetros |
| E8 a E10, P4 | No se realiza | — | — |
| E11 Reporte | Pendiente | — | Se reporta como factibilidad |

## 7. M4. Mortalidad neonatal (NCHS)

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | Archivos públicos de NCHS 2017/2016 a 2024/2023; acuerdo de uso de datos; celdas menores de 10 suprimidas | Datos fuera del repositorio |
| E2 Universo y población | Sí | 8 cohortes de nacimiento (2016–2023): 3,59 a 3,94 millones de nacidos vivos de 22 semanas o más por cohorte; 100% de las muertes infantiles unidas a su nacimiento | Madres residentes en EE. UU., 22 a 47 semanas |
| E3 Definición del desenlace | Sí | 9.574 a 11.497 muertes de 0 a 27 días por cohorte (5.164 a 5.850 con 28 semanas o más) | Etiqueta principal 0–27 días; sensibilidades: 0–6 días, *landmark* a 48 h (originalmente 24 h; M4-D1), 28 semanas o más |
| E4 Auditoría de conteos | Sí | 29.829.867 nacidos vivos y 82.559 muertes neonatales; el 46,6% de las muertes en el 0,59% de nacimientos de 22–27 semanas. Las muertes del día 1 cambian de codificación entre 2016 y 2019; el estado civil deja de reportarse en ~12% desde 2017; el resto de candidatos con ≤3% de desconocidos salvo el intervalo entre embarazos (11,6%) | Decisiones M4-D1 (*landmark* a 48 h), M4-D2 (sin estado civil), M4-D3 (imputación sin indicadores de faltante; registros completos como sensibilidad) |
| P1 Definición | Pasa | Conteos del adaptador reproducidos en las 8 cohortes | — |
| E5 Unidad de análisis | Sí | Sin identificador de la madre | Nacimiento; el grupo es el propio nacimiento |
| E6 Punto de predicción | Sí | — | Nacimiento + 5 min; excluido todo lo posterior (UCI neonatal, ventilación, Apgar a los 10 min) |
| P2 Fuga | Pasa | Nada posterior al punto de predicción; sin indicadores de faltante; sin estado civil. El número de desconocidos por sí solo da AUROC 0,601 (0,571 en 28 semanas o más). XGBoost con faltantes propios agrega 0,002 | Sensibilidad con registros completos |
| E7 Predictores | Sí | 71 parámetros; faltantes de 0% a 3% salvo el intervalo entre embarazos (12,7%) | Tabla contrato; modelo de referencia: edad gestacional + peso al nacer |
| P3 Tamaño de muestra | Pasa | Desarrollo 2016–2021: 22.578.517 nacimientos, 62.713 eventos (883 por parámetro). Planeación (R² supuesto 0,0056): 113.174 nacimientos y 315 eventos requeridos. Reevaluación: R² 0,0201 (53% del máximo); 37.427 nacimientos y 104 eventos requeridos | — |
| E8 Corrida preliminar | Sí | AUROC en la parte de comparación: LR 0,958, RF 0,960, ET 0,962, XGBoost 0,964 (0,962–0,966); ensamble 0,963 (−0,0007, no adoptado) | Algoritmo principal: XGBoost (regla de selección) |
| E8 Evaluación formal | Sí | Validación cruzada 3 × 5 recalibrada: XGBoost 0,964 (0,963–0,965), AUPRC 0,492, pendiente 0,98, O/E 0,99; 91,5% de las muertes en el 10% superior. RL 0,958. Referencia (edad gestacional + peso) 0,907; diferencia +0,057 (0,056–0,058). Temporal 2022–2023: 0,961 (0,959–0,962), pendiente 1,01 (0,97–1,03), O/E 1,05 | — |
| E9 Sensibilidad | Sí | 28 semanas o más 0,936 (referencia 0,829); 48 h y 28 semanas o más 0,893; neonatal precoz 0,981; registros completos 0,959 (reajustado 0,960); cohortes 2020–2021: 0,964, O/E 0,94 | — |
| P4 Clasificación | Sí | C1: 55% de las muertes en los días 0 y 1; a las 48 h AUROC 0,932 (caída 0,032; 0,031–0,033) → cumple con el framework 1.3; ganancia de AUPRC conservada 26% (25–27%), que con el 1.2 (exigía 67%) no cumplía. C2 cumple. C3 no aplica. C4 cumple | **Predictor** (framework 1.3, 23/11/2026, regla adoptada después de ver este resultado; con la 1.2, identificador temprano) |
| E10, E11 | Pendiente | — | — |

## 8. M5. Mortalidad perinatal (NCHS)

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | Archivos públicos de NCHS: nacimientos vinculados con defunciones (cohortes 2016–2023) y muertes fetales (2016–2023) | Datos fuera del repositorio |
| E2 Universo y población | Sí | 29.963.276 productos de 22 a 47 semanas de madres residentes en EE. UU. (29.829.867 nacidos vivos y 133.409 muertes fetales) | Una fila por producto |
| E3 Definición del desenlace | Sí | 194.174 eventos (6,5 por 1.000): 133.409 muertes fetales y 60.765 muertes de 0 a 6 días | Muerte fetal de 22 semanas o más o muerte de 0 a 6 días |
| E4 Auditoría de conteos | Sí | Los desconocidos son de 5 a 18 veces más frecuentes en el reporte de muerte fetal; varias variables dependen de la duración del embarazo | Decisiones D3 a D5 |
| P1 Definición | Pasa | Conteos del adaptador reproducidos en los 8 años | — |
| E5 Unidad de análisis | Sí | Sin identificador de la madre | Producto; el grupo es la propia unidad |
| E6 Punto de predicción | Sí | — | D2 = opción A, prenatal. Si queda como *señal insuficiente*, la opción B (al ingreso) queda declarada como retorno R3 |
| P2 Fuga | Pasa | Sin variables del parto ni excluidas por D4; sin indicadores de faltante. El número de desconocidos por sí solo da AUROC 0,639 | Sensibilidad obligatoria con solo registros completos |
| E7 Predictores | Sí | 24 parámetros; faltantes entre 0% y 4,5% | Tabla contrato |
| P3 Tamaño de muestra | Pasa | R² de Cox-Snell observado 0,0058 (7,6% del máximo); se requerían 37.147 productos y 245 eventos; hay 22.681.046 y 149.057 | Modelo principal: mejor modelo base de una corrida preliminar |
| E8 Modelado | Sí | Corrida preliminar con búsqueda aleatoria de 20 combinaciones por modelo: XGBoost 0,739, bosque aleatorio 0,737, árboles extra 0,732, regresión logística 0,722; ensamble no adoptado. Evaluación formal (5 × 3 particiones, recalibrada): XGBoost AUROC 0,739 (0,737–0,740), AUPRC 0,058, pendiente 1,03, 41,2% de los eventos en el 10% superior | Modelo principal: XGBoost |
| E9 Sensibilidad | Sí | Solo registros completos 0,689; 28 semanas o más 0,712; solo muertes fetales 0,753; anteparto 0,733; neonatales precoces 0,708. Diagnóstico de fuga: XGBoost con faltantes propios 0,768 | — |
| P4 C1 / C2 / C3 / C4 | — | C1 por diseño; C2 no cumple (0,739 < 0,75); C3 no aplica; C4 cumple con el framework 1.1 (AUROC temporal 0,735, pendiente 0,98 dentro de 0,90–1,10, observados/esperados 0,90); con la regla de la versión 1.0 no cumplía (el IC de la pendiente 0,93–0,98 no incluye 1) | — |
| **P4 Clasificación** | **Señal insuficiente** | AUROC por debajo de 0,75 | Retorno R4 |
| R4 (detalle en el anexo de la monografía) 28 semanas o más, registros completos, 28 predictores más finos (agrega diabetes gestacional, hipertensión gestacional, eclampsia y tabaquismo en el embarazo) | Sí | 20.323.696 productos y 56.514 eventos (tienen registro completo el 63% de los eventos frente al 89% de los productos); P2 y P3 pasan (R² 0,00116); XGBoost 0,676 (0,674–0,679), RL 0,660; temporal 0,677, pendiente 0,98; 31% de los eventos en el 10% superior (3,1×). Sensibilidades: todos los registros con imputación por la mediana 0,715; muertes fetales 0,690; neonatales precoces 0,647 | **Señal insuficiente** (no cumple C2 ni C4: AUROC < 0,75 y AUROC temporal < 0,70). Las variables nuevas aportan poco (≥28 semanas: 0,712 en la versión 1 frente a 0,715); la caída viene de excluir los productos de 22–27 semanas y los registros con desconocidos |
| Versión 2: versión 1 + diabetes gestacional, hipertensión gestacional, eclampsia y tabaquismo en el embarazo (decisión del equipo de modelado, D4 revisada) | Sí | 28 parámetros; P2 y P3 pasan (R² 0,00592). XGBoost 0,742 (0,741–0,743), RL 0,724; ensamble −0,0006, no adoptado; temporal 0,738, pendiente 0,98, observados/esperados 0,90; 41,7% de los eventos en el 10% superior (4,2×). Sensibilidades: registros completos 0,693; 28 semanas o más 0,714; muertes fetales 0,756; neonatales precoces 0,711. Razones de odds ajustadas, todos los productos frente a 28 semanas o más: diabetes gestacional 0,66 frente a 0,96; hipertensión gestacional 0,85 frente a 0,98; eclampsia 1,30 frente a 1,29; tabaquismo 1,42 frente a 1,42 | **Señal insuficiente**: cumple C4, no cumple C2 (0,742 < 0,75). Ganancia de 0,003 sobre la versión 1. El efecto protector aparente de la diabetes y la hipertensión gestacionales viene de los productos de 22–27 semanas y desaparece desde las 28 semanas |
| E10, E11 | Pendiente | — | — |
| Versión 2b: versión 2 + talla materna, cigarrillos en el primer trimestre, medicamentos de fertilidad, reproducción asistida, sexo del producto y mes de la última menstruación (completa el segundo retorno) | Sí | 35 parámetros; XGBoost 0,7455 (0,744–0,747); temporal 0,742, pendiente 0,98; 42,4% de los eventos en el 10% superior. Sensibilidades: + edad del padre 0,748; solo muertes fetales 0,760; neonatales precoces 0,713; registros completos 0,689 | **Señal insuficiente** (a 0,0045 del umbral) |
| Verificación de P2 sobre la imputación (notebook 10) | Sí | Mismas particiones: mediana 0,7455; sorteo aleatorio 0,7234 (diferencia −0,022; −0,023 a −0,022); mediana con indicadores 0,770; nativa 0,770. En registros completos las cuatro dan 0,689–0,691: la diferencia está solo en los registros con desconocidos. Los datos imputados con la mediana revelan qué registro tenía un desconocido con AUROC 0,913 | **La imputación por la mediana llevaba señal del tipo de formulario**: el AUROC limpio de la versión 2b es 0,723. La regla fijada antes de correr manda usar el sorteo aleatorio en adelante. **Modelo cerrado** (decisión del 24/11/2026): no se construye la versión 3, porque las variables prenatales comunes a los dos archivos que quedan no pueden cerrar una brecha de 0,027 |

## 9. M6. Muerte fetal de 22 semanas o más (NCHS)

Modelo de verificación de M5: comprueba si el AUROC de 0,760 de las muertes fetales en M5 venía de la señal del tipo de formulario (los desconocidos son mucho más frecuentes en el reporte de muerte fetal). Repositorio: [FetalDeath_NCHS](https://github.com/Tesis-Doctoral/FetalDeath_NCHS).

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 a E4, P1 | Sí | Población de M5 (22 semanas o más): 133.409 muertes fetales; registros completos: 97–98% de los nacidos vivos y 76–81% de las muertes fetales | Decisiones M6-D1 a M6-D4: solo variables prenatales con 10% o menos de desconocidos en el reporte fetal (22 parámetros); sin condiciones que dependen de la duración del embarazo; análisis principal con registros completos |
| E6 Punto de predicción | Sí | — | Prenatal |
| P2 Fuga | Pasa | El número de desconocidos por sí solo da AUROC 0,591 en todos los registros y 0,5 en los registros completos | **Prueba explícita de la señal del formulario** |
| P3 Tamaño de muestra | Pasa | 22.127.472 productos y 81.454 eventos de desarrollo (3.702 por parámetro); R² 0,00235; se requerían 84.109 productos | — |
| E8 Modelado | Sí | Corrida preliminar: RF 0,681, XGBoost 0,680, ET 0,680, LR 0,671; principal XGBoost por la regla. Evaluación formal: 0,680 (0,678–0,682), pendiente 0,97; 32,8% de los eventos en el 10% superior (3,3×). Temporal 0,673, pendiente 0,98 | Algoritmo principal: XGBoost |
| E9 Sensibilidad | Sí | + diabetes e hipertensión gestacionales 0,682; 35 variables con registros completos 0,697; todos los registros imputados 0,683 (incluye la señal del formulario); anteparto 0,672 | — |
| P4 Clasificación | Sí | C1 por diseño; C2 no cumple (0,680); C3 no aplica; C4 no cumple (temporal 0,673) | **Señal insuficiente**. Con la fuga controlada, la muerte fetal prenatal no supera 0,70: el 0,760 de M5 venía en buena parte del tipo de formulario |
| E10, E11 | Pendiente | — | Se reporta como validación de la puerta P2 |

## 10. Equivalencias con la numeración anterior

La numeración se revisó el 24/11/2026 para que el catálogo de la monografía no tenga huecos. Los repositorios no cambian de nombre. Las salidas agregadas generadas antes de esa fecha (`data/outputs_*.txt`) conservan la numeración anterior.

| Numeración actual | Numeración anterior | Modelo |
|---|---|---|
| M1 | M1 | Muerte materna (MIMIC-IV) |
| M2 | M2 | Morbilidad materna grave (MIMIC-IV) |
| M3 | M3 | Muerte fetal al ingreso + 6 h (MIMIC-IV) |
| M3 · R3 | M4 | Muerte fetal prenatal (MIMIC-IV), retorno R3 de M3 |
| M4 | M5 | Mortalidad neonatal (NCHS); decisiones M5-D1 a M5-D3 → M4-D1 a M4-D3 |
| M5 | M6 | Mortalidad perinatal (NCHS) |
| M6 | M7 | Muerte fetal de 22 semanas o más (NCHS); decisiones M7-D1 a M7-D4 → M6-D1 a M6-D4 |

*Confidencial: uso interno del proyecto de investigación.*
