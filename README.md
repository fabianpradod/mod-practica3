# mod-practica3

Práctica 3 de **CC3074 Modelación y Simulación**, Ciclo 2, 2026. Universidad del Valle de Guatemala.

A\*, Hill Climbing, Flujo Máximo y Costo Mínimo implementados desde cero, solo con la librería estándar de Python y `matplotlib`.

## Contenido

| Archivo | Descripción |
|---|---|
| [`Practica_3_AStar_Hill_Climbing_Redes.ipynb`](Practica_3_AStar_Hill_Climbing_Redes.ipynb) | Solución completa de las 24 actividades, preguntas de análisis y tabla de integración, ejecutado. |
| [`Practica_3_AStar_Hill_Climbing_Redes_notes.md`](Practica_3_AStar_Hill_Climbing_Redes_notes.md) | Notas de trabajo: datos usados, supuestos, decisiones, hallazgos y lo aprendido. |
| [`Practica_3.md`](Practica_3.md) | Enunciado original de la práctica. |
| [`figuras/`](figuras) | Gráficas que genera el notebook. |

## Resultados

| Parte | Problema | Resultado | Referencia contra la que se valida |
|---|---|---|---|
| 1 | Agente viajero con Hill Climbing | Desde el tour inicial se detiene en 64 (óptimo local); con 100 reinicios llega a **63** | Fuerza bruta de los 720 órdenes: 20 tours válidos, óptimo 63 |
| 2 | Ruta más corta de O a T con A\* | **O-A-B-D-T, costo 13** | A\* con h = 0 (Dijkstra) y las 28 rutas simples de O a T |
| 3A | Flujo máximo de S a T con Edmonds-Karp | **14 unidades** | Corte mínimo A-D, B-D, E-D, E-T con capacidad 14 |
| 3B | Costo mínimo para ese flujo | **14 unidades con costo 88** | Edmonds-Karp con otro orden de tramos envía lo mismo a 89 |

Fuera del notebook, todos los resultados se compararon además contra `networkx`, que el enunciado no permite usar dentro.

## Metodología

El notebook sigue una estructura fija: las secciones 2 a 6 solo definen funciones (datos, preparación, funciones objetivo y heurísticas, algoritmos, gráficas) y la sección 7 las ejecuta paso a paso. Después de cada resultado hay una interpretación con los números obtenidos. Al inicio, una tabla indica en qué paso está cada actividad del enunciado. Los registros por iteración se imprimen como tablas de texto. Los reinicios aleatorios usan una semilla fija, así que el notebook es reproducible.

## Ejecución

Solo requiere `matplotlib` y `jupyter`:

```
pip install matplotlib jupyter
jupyter notebook Practica_3_AStar_Hill_Climbing_Redes.ipynb
```
