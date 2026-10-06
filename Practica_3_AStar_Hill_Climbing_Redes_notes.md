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
- Hill Climbing solo acepta mejoras estrictas y desempata por el primer vecino generado | es la definición de máximo descenso del enunciado y deja el resultado reproducible | aceptar empates, que puede ciclar entre un tour y su reverso.
- Tours al azar para los reinicios por rechazo: barajar las ciudades 2 a 7 hasta que el tour sea válido | da cada uno de los 20 tours válidos con la misma probabilidad, y eso permite calcular la probabilidad de éxito por reinicio | arrancar desde tours no válidos con penalización, que cambia la función objetivo.
- Fuerza bruta de los 720 órdenes como referencia de la Parte 1 | es barata y da el óptimo exacto | comparar solo contra el tour inicial, que no dice si 64 es bueno.
- Ciudades dibujadas en un círculo | el enunciado no da coordenadas y un círculo no sugiere distancias falsas | inventar coordenadas.

## Hallazgos

- Resultados esperados calculados fuera del notebook con fuerza bruta y `networkx`: tour óptimo 63, ruta O a T de costo 13, flujo máximo 14, costo mínimo 88.
- Parte 1: el tour inicial mide 69 y es el peor de los 20 tours válidos. Solo 5 de sus 15 vecinos son válidos.
- Parte 1: Hill Climbing desde el tour inicial baja 69 a 65 a 64 y se detiene en 1-2-4-6-5-3-7-1, un óptimo local. El óptimo global es 63 (1-2-4-6-7-5-3-1 y su reverso).
- Parte 1: con 100 reinicios (semilla 42) el mejor es 63; 22 de 100 reinicios llegan ahí, 34 a 64 y 44 a 65. Solo 6 de 20 arranques válidos (30%) llevan al óptimo: con 10 reinicios hay 97.2% de encontrarlo.
- Parte 1: 22 de 100 está bajo lo esperado (30); con p = 0.3, 22 o menos pasa en cerca del 5% de las corridas (calculado con `scipy` fuera del notebook).

## Aprendido

- Un óptimo local puede estar a dos movimientos del global con el paso intermedio prohibido: aquí pasar de 64 a 63 exige dos inversiones y la primera usa la carretera inexistente 5-1.
- En un problema simétrico, invertir todo el tramo libre da el mismo ciclo al revés con la misma distancia; ese vecino nunca mejora.
- Los empates importan en Hill Climbing: con el otro vecino de 65 en el primer paso se habría detenido en 65.

## Pendientes y dudas

## Registro

- 2026-10-06: notebook creado
- 2026-10-06: configuración limitada a las librerías permitidas por el enunciado
- 2026-10-06: Parte 1 resuelta, verificada contra fuerza bruta independiente (12 de 12 chequeos)
