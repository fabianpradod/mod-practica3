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
- A\* con entradas viejas en el heap que se ignoran al salir (borrado perezoso) y sin reabrir nodos cerrados | `heapq` no permite bajar la prioridad de una entrada, y con heurística consistente cerrar es seguro | buscar y reemplazar la entrada dentro del heap.
- Empates de f en A\* por menor h y luego por nombre | determinista y prefiere el nodo más cerca de la meta | orden de inserción.
- `a_estrella` recibe un parámetro `h` opcional | la misma función sirve para h = 0 y h\* sin duplicar código | una función aparte para Dijkstra.
- Agregar la heurística perfecta h\*(n) (costo real con Dijkstra) | con la euclidiana A\* y Dijkstra expanden lo mismo (7), y h\* muestra con números cuándo una heurística sí ahorra (5) | dejar solo la comparación pedida, que no ilustraba el ahorro.
- Fuerza bruta de rutas simples como referencia de la Parte 2 | confirma el óptimo y deja ver el empate entre dos rutas | solo comparar contra h = 0.
- Red residual como diccionario `residual[u][v]` con la arista de regreso en la misma tabla, rechazando pares de tramos opuestos | es la forma más simple de leer y basta porque esta red no tiene tramos opuestos | listas paralelas con índices de arista, necesarias solo si hubiera u-v y v-u.
- Referencia de la Parte 3A: corte mínimo de la red residual final | si su capacidad iguala al flujo, el flujo es máximo sin necesidad de otra herramienta | comparar contra `networkx` dentro del notebook, prohibido.
- La verificación de la actividad 17 termina con `assert` | si algo falla el notebook se detiene en vez de solo imprimir "no" | solo imprimir la tabla.
- Costo mínimo por caminos sucesivos con Bellman-Ford | acepta los costos negativos de las aristas de regreso y es fácil de seguir por iteración | Dijkstra con potenciales, más rápido pero más difícil de explicar.
- Arista de regreso con costo -c | devolver una unidad descuenta lo que costó mandarla, y eso permite cambiar una ruta por otra más barata | costo 0 en el regreso, que haría creer que deshacer es gratis.
- El extra de la Parte 3B usa Edmonds-Karp con los tramos ordenados por costo ascendente en vez de comparar contra el Edmonds-Karp del enunciado | con el orden del enunciado Edmonds-Karp cae justo en la distribución de costo mínimo (88), y con los tramos baratos primero da 89 y usa una arista de regreso | armar una distribución a mano.

## Hallazgos

- Resultados esperados calculados fuera del notebook con fuerza bruta y `networkx`: tour óptimo 63, ruta O a T de costo 13, flujo máximo 14, costo mínimo 88.
- Parte 1: el tour inicial mide 69 y es el peor de los 20 tours válidos. Solo 5 de sus 15 vecinos son válidos.
- Parte 1: Hill Climbing desde el tour inicial baja 69 a 65 a 64 y se detiene en 1-2-4-6-5-3-7-1, un óptimo local. El óptimo global es 63 (1-2-4-6-7-5-3-1 y su reverso).
- Parte 1: con 100 reinicios (semilla 42) el mejor es 63; 22 de 100 reinicios llegan ahí, 34 a 64 y 44 a 65. Solo 6 de 20 arranques válidos (30%) llevan al óptimo: con 10 reinicios hay 97.2% de encontrarlo.
- Parte 1: 22 de 100 está bajo lo esperado (30); con p = 0.3, 22 o menos pasa en cerca del 5% de las corridas (calculado con `scipy` fuera del notebook).
- Parte 2: la heurística euclidiana es admisible (63% a 72% del costo real) y consistente en los 12 caminos; la holgura mínima es 0.29 en B-E.
- Parte 2: A\* encuentra O-A-B-D-T con costo 13 (2 + 2 + 4 + 5) y expande los 7 nodos. Con h = 0, misma ruta y mismo costo, también 7 nodos en otro orden. Con h\* perfecta, 5 nodos.
- Parte 2: la euclidiana no ahorra porque todos los nodos tienen g\*(n) + h(n) entre 9.01 y 11.54, debajo del óptimo 13.
- Parte 2: hay 28 rutas simples de O a T y dos óptimas empatadas en 13: O-A-B-D-T y O-A-B-E-D-T.
- Parte 3A: Edmonds-Karp llega a flujo máximo 14 en 5 caminos aumentantes (S-A-D-T 3, S-B-D-T 4, S-B-E-T 3, S-C-E-T 3, S-C-E-D-T 1), sin usar aristas de regreso. 7 de 12 tramos saturados; A-B y B-C sin flujo; D-T en 8 de 9.
- Parte 3A: corte mínimo {S, A, B, C, E} contra {D, T}: A-D 3 + B-D 4 + E-D 1 + E-T 6 = 14. Cortes obvios: salida de S 16, entrada a T 15.
- Parte 3A: la distribución de Edmonds-Karp cuesta 88, lo mismo que el costo mínimo.
- Parte 3B: costo mínimo 88 para 14 unidades con 5 rutas: S-A-D-T (6, 3 u), S-B-D-T (6, 4 u), S-B-E-T (6, 3 u), S-C-E-T (7, 3 u), S-C-E-D-T (7, 1 u). No usa aristas de regreso. Pidiendo 15 solo envía 14.
- Parte 3B: al inicio hay 6 rutas de costo 6 por unidad; Bellman-Ford toma la primera que encuentra.
- Parte 3B: Edmonds-Karp con los tramos ordenados por costo ascendente envía 14 en 7 caminos con costo 89; su último camino S-C-E-B-D-T usa la arista de regreso E-B. Sin aristas de regreso esa misma corrida se queda en 13 (comprobado fuera del notebook).
- Parte 3: enumerando todas las distribuciones enteras con flujo 14 (fuera del notebook, unos 121 millones de combinaciones) hay exactamente 4: costos 88, 89, 89 y 90. La de costo 88 es única.

## Aprendido

- Un óptimo local puede estar a dos movimientos del global con el paso intermedio prohibido: aquí pasar de 64 a 63 exige dos inversiones y la primera usa la carretera inexistente 5-1.
- En un problema simétrico, invertir todo el tramo libre da el mismo ciclo al revés con la misma distancia; ese vecino nunca mejora.
- Los empates importan en Hill Climbing: con el otro vecino de 65 en el primer paso se habría detenido en 65.
- A\* tiene que expandir todo nodo con g\*(n) + h(n) menor que el costo óptimo. Una heurística admisible pero floja no descarta nada en una red pequeña donde todo queda en dirección a la meta.
- A\* termina cuando la meta sale de la lista abierta, no cuando aparece: aquí T apareció primero con 14 y luego bajó a 13.
- Heurística consistente: |h(u) - h(v)| <= w(u, v) en cada arista; junto con h(meta) = 0 implica admisible.
- El resultado de Edmonds-Karp depende del orden en que BFS revisa los vecinos: con el mismo flujo máximo puede quedar una distribución barata o cara, porque no mira costos.
- Las aristas de regreso no son un detalle técnico: en la corrida con tramos por costo, sin ellas el algoritmo se queda en 13 en vez de 14.
- En caminos sucesivos de menor costo el costo por unidad nunca baja (6, 6, 6, 7, 7): se agotan primero las rutas baratas.
- Ampliar un tramo que no está en el corte mínimo no aumenta el flujo máximo, porque ese corte sigue igual.

## Pendientes y dudas

## Registro

- 2026-10-06: notebook creado
- 2026-10-06: configuración limitada a las librerías permitidas por el enunciado
- 2026-10-06: Parte 1 resuelta, verificada contra fuerza bruta independiente (12 de 12 chequeos)
- 2026-10-06: Parte 2 resuelta, verificada contra `networkx` fuera del notebook (12 de 12 chequeos)
- 2026-10-06: Parte 3A resuelta, verificada contra `networkx` fuera del notebook (9 de 9 chequeos)
- 2026-10-06: Parte 3B resuelta, verificada contra `networkx` fuera del notebook (9 de 9 chequeos, incluye objetivo de 5 unidades)
