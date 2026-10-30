# Detección de fauna en cámaras trampa con YOLOv8

Pregunta 3 del examen parcial de Redes Neuronales y Aprendizaje Profundo (UNI FIIS, 2026-II).
Se ajustan dos detectores YOLOv8-s sobre Caltech Camera Traps: **A**, con la partición oficial
`eccv_train` de CCT-20 (10 cámaras), y **B**, que añade las cámaras externas del conjunto completo
con cajas de las 15 clases. Ambos se evalúan en cámaras vistas (cis), en las 9 cámaras nuevas de la
partición oficial (trans), en 30 cámaras nuevas adicionales (trans ampliado) y en ENA24-detection
(otra región). El informe en formato NeurIPS está en `informe/main.pdf`.

## Ejecución (un solo comando)

Con el entorno instalado (`pip install -r requirements.txt`), todo el estudio se reproduce con:

```bash
jupyter nbconvert --to notebook --execute P3_CamarasTrampa_YOLOv8.ipynb --output P3_ejecutado.ipynb --ExecutePreprocessor.timeout=-1
```

El cuaderno descarga los datos desde LILA BC, construye los conjuntos, entrena A y B, evalúa los
cuatro dominios y escribe tablas, figuras (PDF) y `metricas.tex` en `p3_work/resultados/`.

**En Google Colab** (configuración usada: GPU T4): abrir el cuaderno y usar
*Entorno de ejecución → Ejecutar todas*. El trabajo persistente se guarda en
`MyDrive/P3_CCT20/`; si la sesión se cae, basta con volver a ejecutar todo: cada etapa deja una
marca de estado, los entrenamientos se reanudan desde `last.pt` y las descargas e inferencias
continúan por bloques.

**Verificación rápida del flujo.** Con `MODO_PRUEBA = True` en la primera celda de código, el
cuaderno genera un conjunto sintético con la estructura de CCT y recorre todo el flujo en CPU en
pocos minutos.

## Requisitos de cómputo

| Etapa | Tiempo aproximado en T4 |
|---|---|
| Descarga y preparación de datos | variable, según la red |
| Entrenamiento A (parada temprana, época 37) | 2.6 h |
| Entrenamiento B (parada temprana, época 45) | 2.2 h |
| Inferencia y análisis | ~1 h |

## Estructura

```
├── P3_CamarasTrampa_YOLOv8.ipynb   cuaderno completo, con las salidas de la ejecución reportada
├── requirements.txt                especificación del entorno
├── pesos/
│   ├── A_best.pt                   pesos seleccionados del modelo A
│   └── B_best.pt                   pesos seleccionados del modelo B
└── informe/
    ├── main.tex, main.pdf          informe (NeurIPS, biblatex + biber)
    ├── referencias.bib
    ├── figuras/                    diagramas TikZ standalone (.tex y .pdf)
    └── resultados/                 tablas, figuras y métricas generadas por el cuaderno
```

Para recompilar el informe: `cd informe && latexmk -pdf main.tex`.

## Datos

* Caltech Camera Traps y CCT-20: <https://lila.science/datasets/caltech-camera-traps>
* ENA24-detection: <https://lila.science/datasets/ena24detection>

Los datos no se versionan; el cuaderno los descarga.

## Notas de reproducibilidad

* Semilla 0; las listas de imágenes y el orden de las clases se fijan en la primera ejecución.
* El mapeo taxonómico de ENA24 de la ejecución reportada asignó *Sciurus niger* a `fox`; el informe
  lo declara como limitación.
* La validación del estimador de oclusión usa `informe/resultados/oclusion_validacion_lleno.csv`.

## Referencia principal

Beery, S., Van Horn, G., & Perona, P. (2018). Recognition in Terra Incognita. *ECCV 2018*.
