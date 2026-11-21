# Modelos que aplican el framework

Catálogo de todos los modelos ideados en la tesis, incluidos los que no pasaron alguna puerta, con el enlace a su repositorio y el estado de cada etapa y puerta. Actualizado el 21/11/2026. Versión en inglés: [`models.md`](models.md). El framework se describe en [`README_es.md`](README_es.md).

**Convenciones de estado.** **Sí**: la tarea se realizó. **En curso**: empezada, sin terminar. **Pendiente**: aún no se ha hecho, pero se hará. **No se realiza**: no se hará porque el modelo se detuvo en una puerta anterior. **No aplica**: el criterio no corresponde a ese desenlace. En las puertas se indica si **pasa** o **no pasa**, con los indicadores obtenidos y la decisión tomada. AUROC con su IC 95% entre paréntesis.

## 1. Resumen

| Id | Modelo | Desenlace | Fuente | Punto de predicción | Hasta dónde llegó | Resultado | Repositorio |
|---|---|---|---|---|---|---|---|
| M1 | Muerte materna | Muerte en la hospitalización de parto | MIMIC-IV v3.1 | Ingreso + 24 h | P3 | **No pasa P3** (3 eventos): factibilidad; retorno R1 a M2 | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M2 | Morbilidad materna grave (SMM) | 20 indicadores de los CDC | MIMIC-IV v3.1 | Ingreso + 24 h | E11 | **Identificador temprano**; retorno R2 (escalamiento): **señal exploratoria de predicción** | [MaternalMortality_MIMIC-IV_Cureus](https://github.com/Tesis-Doctoral/MaternalMortality_MIMIC-IV_Cureus) |
| M3 | Muerte fetal al ingreso | Muerte fetal (códigos CIE de la madre) | MIMIC-IV v3.1 | Ingreso + 6 h | E6 | Detenido antes de modelar: 73 de 129 muertes ya presentes en el punto de predicción; retorno R3 a M4 | [PerinatalMortality_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalMortality_MIMIC-IV) |
| M4 | Muerte fetal prenatal | Muerte fetal (códigos CIE de la madre) | MIMIC-IV v3.1 | Ingreso (información de 7 días antes o más) | P3 | **No pasa P3** (R² de Cox-Snell negativo): factibilidad | [PerinatalAntenatal_MIMIC-IV](https://github.com/Tesis-Doctoral/PerinatalAntenatal_MIMIC-IV) |
| M5 | Mortalidad neonatal | Muerte de 0 a 27 días | NCHS (CDC), nacimientos vinculados con defunciones, cohortes 2016–2023 | Nacimiento + 5 min | E3 | En curso: adaptador de datos listo; sigue la auditoría de conteos | [NeonatalMortality_NCHS](https://github.com/Tesis-Doctoral/NeonatalMortality_NCHS) |
| M6 | Mortalidad perinatal | Muerte fetal de 22 semanas o más o muerte neonatal de 0 a 6 días | NCHS (CDC) | Prenatal (opción A) | E6 | En curso: falta el archivo de muertes fetales (decisión D1) | [PerinatalMortality_NCHS](https://github.com/Tesis-Doctoral/PerinatalMortality_NCHS) |

Repositorio de este framework: [StagedFramework_ClinicalPrediction](https://github.com/Tesis-Doctoral/StagedFramework_ClinicalPrediction). Todos los repositorios están en la organización [Tesis-Doctoral](https://github.com/Tesis-Doctoral).

## 2. Cómo se relacionan

```
Ranura materna      M1 Muerte materna ──R1──► M2 SMM ──R4──► laboratorios ampliados
                                                     └─R2──► escalamiento de la atención ──► reducción por tamaño de muestra
Ranura perinatal    M3 Muerte fetal al ingreso + 6 h ──R3──► M4 Muerte fetal prenatal
                    M6 Mortalidad perinatal (NCHS)
Ranura neonatal     M5 Mortalidad neonatal (NCHS)
```

## 3. M1. Muerte materna (MIMIC-IV)

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

## 4. M2. Morbilidad materna grave a las 24 horas (MIMIC-IV)

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
| P3 Tamaño de muestra | Pasa | R² de Cox-Snell observado 0,0311 (máximo 0,196); se requerían 9.112 admisiones y 209 eventos; había 19.383 y 445; soporta hasta 68 parámetros | Modelo principal: XGBoost |
| E8 Modelado | Sí | XGBoost: AUROC 0,791 (0,767–0,816), bien calibrado | — |
| E9 Sensibilidad | Sí | Definiciones, poblaciones, laboratorios, variables de UCI, remuestreo | — |
| P4 C1 Eventos presentes | No cumple | AUROC *landmark* 0,723 (0,690–0,753): cae 0,068; conserva el 38% de la ganancia del AUPRC | — |
| P4 C2 Desenlace con tiempo | Limítrofe | Incidentes documentados 0,745 (0,704–0,788); incidentes estrictos 0,798 (0,748–0,850) | — |
| P4 C3 Sesgo de detección | No cumple | Falla renal: AUROC 0,850 en todas, 0,628 entre las examinadas | — |
| P4 C4 Estabilidad | No cumple | AUROC temporal 0,747 (0,707–0,783); pendiente de calibración 0,83 (0,71–0,97); observados/esperados 1,34 | — |
| **P4 Clasificación** | **Identificador temprano** | Falla C1, C3 y C4 | Retornos R4 y R2 |
| E10 Acción | Sí | Marcar el 5% de mayor riesgo: unas 970 alertas, valor predictivo positivo 16,4%, unas 6 revisiones por caso | Vigilancia y priorización, no prevención |
| E11 Reporte | Sí | Manuscrito y lista TRIPOD+AI | — |

### Retornos y reducción

| Paso | Realizada | Indicadores | Decisión |
|---|---|---|---|
| R4 Laboratorios ampliados (10 exámenes con indicador de faltante) | Sí | AUROC 0,821 (0,799–0,844); *landmark* 0,737; sin indicadores de faltante 0,726; C3: 0,861 baja a 0,643; C4: AUROC temporal *landmark* 0,697, pendiente 0,73 | **Sigue como identificador temprano**: la ganancia venía de qué exámenes se pidieron |
| R2 Escalamiento de la atención después de las 24 h | Sí | 101 eventos en 19.268 partos (0,52%); AUROC 0,763 (0,709–0,808); 46,5% de los eventos en el 10% superior; C1: 0,761; C2 cumple; C3 no aplica; C4: AUROC temporal 0,714 (0,629–0,795), pendiente 0,98 (0,60–1,31) | Cumple P4, pero **no pasa P3**: R² 0,0077; se requerían 37.098 admisiones; soporta 16 parámetros de 32 |
| Reducción por tamaño de muestra (13 parámetros) | Sí | P3 **pasa**: R² 0,0069, se requerían 16.962 admisiones y 89 eventos. C1 cumple (0,754 frente a 0,749). XGBoost: C2 0,749 (0,692–0,798), C4 0,699 (0,620–0,778), justo bajo los umbrales; la regresión logística sí los cumple (0,764; 0,712) | **Señal exploratoria de predicción (limítrofe)**; el modelo principal no se cambió después de ver resultados |

## 5. M3. Muerte fetal al ingreso + 6 h (MIMIC-IV)

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
| **Decisión** | — | Más de la mitad de los eventos ya estaba presente al predecir | **Retorno R3 anticipado** (otro punto de predicción): M4. El modelo a las 6 h no se ajustó |
| E11 Reporte | Pendiente | — | Se reporta la auditoría y la razón del retorno |

## 6. M4. Muerte fetal prenatal (MIMIC-IV)

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

## 7. M5. Mortalidad neonatal (NCHS)

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | Archivos públicos de NCHS 2017/2016 a 2024/2023; acuerdo de uso de datos; celdas menores de 10 suprimidas | Datos fuera del repositorio |
| E2 Universo y población | Sí | 8 cohortes de nacimiento (2016–2023): 3,59 a 3,94 millones de nacidos vivos de 22 semanas o más por cohorte; 100% de las muertes infantiles unidas a su nacimiento | Madres residentes en EE. UU., 22 a 47 semanas |
| E3 Definición del desenlace | Sí | 9.574 a 11.497 muertes de 0 a 27 días por cohorte (5.164 a 5.850 con 28 semanas o más) | Etiqueta principal 0–27 días; sensibilidades: 0–6 días, *landmark* a 24 h, 28 semanas o más |
| E4 Auditoría de conteos | Pendiente | — | Siguiente paso |
| P1 Definición | Pendiente | Conteos de referencia escritos en `data/outputs_01_build_cohorts.txt` | — |
| E5 Unidad de análisis | Sí | Sin identificador de la madre | Nacimiento; el grupo es el propio nacimiento |
| E6 Punto de predicción | Sí | — | Nacimiento + 5 min; excluido todo lo posterior (UCI neonatal, ventilación, Apgar a los 10 min) |
| P2, E7, P3, E8, E9, P4, E10, E11 | Pendiente | — | — |

## 8. M6. Mortalidad perinatal (NCHS)

| Etapa o puerta | Realizada | Indicadores | Decisión |
|---|---|---|---|
| E1 Gobierno y configuración | Sí | Ficha del desenlace versión 1 | — |
| E2 Universo y población | En curso | Componente neonatal precoz: adaptador listo (unos 7.000 a 8.600 eventos por cohorte). Componente fetal: falta el archivo de muertes fetales | D1 pendiente |
| E3 Definición del desenlace | Sí | — | Muerte fetal de 22 semanas o más o muerte de 0 a 6 días |
| E6 Punto de predicción | Sí | — | D2 = opción A, prenatal; excluye el número de controles y la ganancia de peso (dependen de la duración del embarazo) y todo lo del parto. Si queda como *ninguno*, la opción B (al ingreso) queda declarada como retorno R3 |
| E4, P1, E5, P2, E7, P3, E8, E9, P4, E10, E11 | Pendiente | — | — |

*Confidencial: uso interno del proyecto de investigación.*
