# Notas: Practica_3_AStar_Hill_Climbing_Redes.ipynb

## Objetivo

¿Qué solución encuentran Hill Climbing, A\*, Edmonds-Karp y el flujo de costo mínimo en sus tres problemas, y cómo se comprueba si es la mejor posible?

## Datos usados

| Tabla o archivo | Fuente | Filas x columnas | Fecha de obtención | Notas |
|---|---|---|---|---|
| Carreteras entre ciudades (`get_carreteras`) | Enunciado, Parte 1 | 13 carreteras x 3 (ciudad, ciudad, distancia) | 2026-10-06 | 7 ciudades, doble sentido. No todas las ciudades están conectadas. |
| Caminos del parque (`get_caminos`) | Enunciado, Parte 2 | 12 caminos x 3 (estación, estación, distancia) | 2026-10-06 | Estaciones O, A, B, C, D, E, T, doble sentido. |
| Coordenadas de estaciones (`get_coordenadas`) | Enunciado, Parte 2 | 7 estaciones x 2 (x, y) | 2026-10-06 | Solo para la heurística euclidiana. |
| Tramos de distribución (`get_tramos`) | Enunciado, Parte 3 | 12 tramos x 4 (origen, destino, capacidad, costo) | 2026-10-06 | Dirigidos de S a T. Los costos son didácticos, agregados por el enunciado. |

## Supuestos

- Las distancias de la Parte 1 son simétricas: ir de i a j cuesta lo mismo que de j a i.
- En la Parte 3 los costos son por unidad y lineales, sin costo fijo por usar un tramo.

## Decisiones

Formato: qué se decidió | por qué | alternativa descartada.

- Solo librerías permitidas (estándar, `heapq`, `collections`, `random`, `itertools`, `matplotlib`) | lo exige el enunciado | `numpy` y `pandas` del esqueleto estándar, que se quitaron de la configuración.
- Tablas impresas con `imprimir_tabla` (texto de ancho fijo) | sin `pandas` no hay `DataFrame` | imprimir cada fila con `print` suelto, más difícil de leer.
- Nombres y firmas de las funciones exactamente como el enunciado | el evaluador las busca por nombre | nombres propios en inglés como en el ejercicio de clase.
- Los algoritmos devuelven un registro (lista de filas) y el paso de ejecución lo imprime | separa el cálculo de la presentación y deja revisar el registro después | imprimir dentro del algoritmo.
- `networkx` solo se usó fuera del notebook, para verificar los resultados esperados | el enunciado lo prohíbe dentro | verificar dentro del notebook.

## Hallazgos

- Resultados esperados calculados fuera del notebook con fuerza bruta y `networkx`: tour óptimo 63, ruta O a T de costo 13, flujo máximo 14, costo mínimo 88.

## Aprendido

## Pendientes y dudas

## Registro

- 2026-10-06: notebook creado
- 2026-10-06: configuración limitada a las librerías permitidas por el enunciado
