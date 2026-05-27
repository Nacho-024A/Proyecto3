# Maze Intelligence — Juego de Laberintos en Jack / Hack

Videojuego de laberinto desarrollado en el lenguaje **Jack** para la plataforma **Hack** (nand2tetris). El jugador debe navegar a través de dos laberintos evitando ser capturado por un enemigo controlado por inteligencia artificial basada en BFS (búsqueda en anchura).

---

## Estructura del Proyecto

```
LaberintoJack/
│
├── Main.jack          # Punto de entrada; orquesta el flujo de pantallas y niveles
├── Start.jack         # Pantalla de inicio (menú principal con configuración de velocidad)
├── GameOver.jack      # Pantalla de derrota (el enemigo capturó al jugador)
├── End.jack           # Pantalla de victoria (ambos niveles completados)
├── Maze.jack          # Nivel 1 — mapa, lógica de celdas y renderizado
├── Maze2.jack         # Nivel 2 — mapa más complejo, misma arquitectura
├── Player.jack        # Jugador: posición, movimiento validado y sprite
└── Enemy.jack         # Enemigo: IA con BFS para perseguir al jugador
```

---

## Flujo del Juego

```
Menú de Inicio (Start)
   ↓  seleccionar velocidad + ENTER
Nivel 1 (Maze) — 1 enemigo
   ↓  jugador llega a la salida
   ↓  [si capturado → GameOver]
Transición → ENTER para continuar
   ↓
Nivel 2 (Maze2) — 2 enemigos, más rápidos
   ↓  jugador llega a la salida
   ↓  [si capturado → GameOver]
Pantalla de Victoria (End)
```

---

## Descripción de Clases

### `Main.jack`
Punto de entrada del programa. Orquesta el flujo completo: menú → Nivel 1 → Nivel 2 → fin. Gestiona el game loop de cada nivel, el movimiento del jugador, el ciclo del enemigo, la detección de captura y la condición de victoria (llegar a la celda de salida con valor `2`). Al finalizar cada nivel libera la memoria de todas las entidades instanciadas.

### `Start.jack`
Menú de inicio interactivo con tres opciones navegables con flechas:
- **Iniciar** — comienza el juego.
- **Configuración** — cambia la velocidad del enemigo (Lento / Normal / Rápido).
- **Salir** — cierra el programa.

El título MAZE se dibuja en pixel-art con primitivas de `Screen`. Un triángulo selector indica la opción activa. Al confirmar con ENTER retorna la velocidad seleccionada a `Main`.

Métodos:
- `Start.show()` — muestra el menú, gestiona la navegación y retorna `enemySpeed`.

### `GameOver.jack`
Pantalla de derrota mostrada cuando el enemigo alcanza la misma celda que el jugador. Muestra GAME OVER en letras grandes dibujadas con rectángulos. Espera ENTER para terminar el programa.

Métodos:
- `GameOver.show()` — limpia la pantalla, dibuja y bloquea hasta ENTER.

### `End.jack`
Pantalla de victoria mostrada al completar el Nivel 2. Muestra un mensaje de felicitación y una decoración geométrica. Espera ENTER para terminar el programa.

Métodos:
- `End.show()` — limpia la pantalla, dibuja y bloquea hasta ENTER.

### `Maze.jack` / `Maze2.jack`
Clases que representan los dos niveles del juego. Comparten la misma arquitectura:

**Representación del mapa:**
El mapa se almacena como un array 1D de 512 enteros que simula una grilla 2D de **16 filas × 32 columnas**. Cada celda es un cuadrado de **16×16 píxeles**, ocupando la pantalla completa de 512×256 px.

Valores de celda:
- `0` → Camino libre
- `1` → Muro (rectángulo negro sólido)
- `2` → Salida (condición de victoria al pisarla)

Métodos:
- `new()` — constructor, inicializa el array de 512 celdas.
- `getCell(row, col)` — retorna el valor de una celda.
- `setCell(row, col, value)` — asigna un valor a una celda.
- `canMove(row, col)` — retorna `true` si la celda no es muro (valor ≠ 1).
- `loadMap()` — inicializa el mapa con las coordenadas de muros y salida.
- `draw()` — recorre todas las celdas y las dibuja en pantalla según su valor.
- `dispose()` — libera la memoria del array y del objeto.

### `Player.jack`
Representa al jugador dentro del laberinto. Almacena su posición en fila y columna, valida el movimiento consultando `canMove()` del laberinto antes de moverse, y gestiona el sprite (borra la posición anterior y dibuja en la nueva).

Métodos:
- `new(row, col)` — constructor, inicializa la posición.
- `move(row, col, maze)` — intenta moverse a la celda indicada si es válida.
- `draw(show)` — dibuja (`true`) o borra (`false`) el sprite del jugador.
- `getRow()` / `getCol()` — retornan la posición actual.
- `dispose()` — libera el objeto.

### `Enemy.jack`
Representa a un enemigo que persigue al jugador mediante BFS. Los buffers del algoritmo (`queue`, `visited`, `parent`) se pre-asignan en el constructor y se reutilizan en cada ciclo para evitar fragmentación del heap.

Métodos:
- `new(row, col)` — constructor, inicializa posición y pre-asigna buffers BFS.
- `move(playerRow, playerCol, maze)` — calcula y ejecuta el siguiente paso óptimo en Nivel 1.
- `move2(playerRow, playerCol, maze2)` — ídem para Nivel 2.
- `isCaught(playerRow, playerCol)` — retorna `true` si el enemigo ocupa la misma celda que el jugador.
- `draw(show)` — dibuja (`true`) o borra (`false`) el sprite del enemigo.
- `dispose()` — libera el objeto y sus buffers.

---

## Niveles

| | Nivel 1 | Nivel 2 |
|---|---|---|
| Clase del mapa | `Maze` | `Maze2` |
| Número de enemigos | 1 | 2 |
| Velocidad del enemigo | `enemySpeed` ciclos | `enemySpeed - 2` ciclos |
| Posición inicial jugador | (1, 1) | (1, 1) |
| Posición inicial enemigo(s) | (14, 28) | (13, 28) y (13, 1) |

---

## Controles

| Tecla | Acción |
|-------|--------|
| ↑ Flecha arriba | Mover jugador hacia arriba |
| ↓ Flecha abajo | Mover jugador hacia abajo |
| ← Flecha izquierda | Mover jugador hacia la izquierda |
| → Flecha derecha | Mover jugador hacia la derecha |
| ENTER | Confirmar / Avanzar de pantalla |

---

## Velocidades del Enemigo

Configurable desde el menú antes de iniciar el juego:

| Opción | Ciclos entre movimientos |
|--------|--------------------------|
| Lento | 8 |
| Normal (por defecto) | 6 |
| Rápido | 4 |

En el Nivel 2 los enemigos se mueven siempre 2 ciclos más rápido que el valor configurado.

---

## Arquitectura General

```
Main
 ├── Start            (menú: retorna enemySpeed)
 ├── Maze             (nivel 1)
 │    ├── Player      (se mueve sobre Maze)
 │    └── Enemy       (persigue al jugador con BFS)
 ├── Maze2            (nivel 2)
 │    ├── Player      (re-instanciado)
 │    ├── Enemy [0]   (esquina derecha)
 │    └── Enemy [1]   (esquina izquierda)
 ├── GameOver         (derrota: enemigo captura al jugador)
 └── End              (victoria: jugador completa el Nivel 2)
```

---

## Compilación y Ejecución

Este proyecto usa el compilador de Jack de **nand2tetris**.

1. Colocar todos los archivos `.jack` en una misma carpeta.
2. Compilar con el JackCompiler:
   ```
   JackCompiler /ruta/a/la/carpeta
   ```
3. Cargar los archivos `.vm` generados en el **VM Emulator** de nand2tetris.
4. Ejecutar desde `Main.main`.

Requisitos: nand2tetris Software Suite (disponible en [nand2tetris.org](https://www.nand2tetris.org)).

Otra opcion valida es:

1. Colocar todos los archivo `.jack` en una misma carpeta.
2. Cargar dicha carpeta en `https://nand2tetris.github.io/web-ide/compiler` desde el editor online de Nand2Tetris.
3. Compilar y correr dentro del mismo Nand2Tetris Web

---

## Detalles Técnicos

| Característica | Valor |
|----------------|-------|
| Lenguaje | Jack (nand2tetris) |
| Plataforma destino | Hack Computer |
| Resolución de pantalla | 512 × 256 px |
| Tamaño de celda | 16 × 16 px |
| Grilla del laberinto | 16 filas × 32 columnas |
| Total de celdas | 512 |
| Almacenamiento del mapa | Array 1D con índice `row * 32 + col` |
| Algoritmo de IA | BFS (Breadth-First Search) con buffers reutilizables |
| Número de niveles | 2 |

---

## Autores

Proyecto desarrollado como parte del curso **Organización de Computadores** — Universidad EAFIT, 2026.

- Andrés David Osorio Moreno
- Daniel Mauricio Giraldo Moreno
