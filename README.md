# Laberinto — Juego en Jack / Hack

Juego de laberinto desarrollado en el lenguaje **Jack** para la plataforma **Hack** (nand2tetris). El jugador debe navegar a través de dos laberintos distintos hasta encontrar la salida marcada en cada nivel.

---

## Estructura del Proyecto

```
LaberintoJack/
│
├── Main.jack          # Punto de entrada; orquesta el flujo de pantallas y niveles
├── Start.jack         # Pantalla de inicio (menú principal)
├── End.jack           # Pantalla de fin (laberinto completado)
├── Maze.jack          # Nivel 1 — mapa, lógica de celdas y renderizado
├── Maze2.jack         # Nivel 2 — mapa más complejo, misma arquitectura
│
├── Player.jack        # (Pendiente) Jugador: posición, movimiento, colisiones
└── Enemy.jack         # (Pendiente) Enemigos: IA de patrullaje, detección
```

---

## Flujo del Juego

```
Pantalla de Inicio (Start)
        ↓  ENTER
     Nivel 1 (Maze)
        ↓  ENTER  ← (provisional, reemplazar por detección de salida)
     Nivel 2 (Maze2)
        ↓  ENTER  ← (provisional)
  Pantalla de Fin (End)
        ↓  ENTER
       Cierre
```

---

## Descripción de Clases

### `Main.jack`
Punto de entrada del programa. Instancia y ejecuta cada pantalla y nivel en orden. Actualmente usa `Keyboard.readChar()` como transición provisional entre niveles; en el futuro esto será reemplazado por la detección del jugador llegando a la celda de salida (`valor 2`).

### `Start.jack`
Pantalla de bienvenida del juego.
- Dibuja el título **LABERINTO** con gráficos pixelados (rectángulos primitivos de la API Screen).
- Muestra las instrucciones de controles y objetivo.
- Espera que el jugador presione **ENTER** (código de tecla `128`) para iniciar.

Métodos:
- `Start.show()` — limpia la pantalla, dibuja y bloquea hasta ENTER.
- `Start.draw()` — renderiza todos los elementos visuales.

### `End.jack`
Pantalla de victoria, mostrada al completar el Nivel 2.
- Dibuja el título **FIN** con letras grandes pixeladas.
- Muestra mensaje de felicitación.
- Espera **ENTER** para terminar el programa.

Métodos:
- `End.show()` — limpia la pantalla, dibuja y bloquea hasta ENTER.
- `End.draw()` — renderiza todos los elementos visuales.

### `Maze.jack` / `Maze2.jack`
Clases que representan los dos niveles del juego. Comparten la misma arquitectura:

**Representación del mapa:**
El mapa se almacena como un array 1D de 512 enteros que simula una grilla 2D de **16 filas × 32 columnas**. Cada celda es un cuadrado de **16×16 píxeles**, ocupando la pantalla completa de 512×256 px.

Valores de celda:
- `0` → Camino libre (fondo blanco con borde negro)
- `1` → Muro (rectángulo negro sólido)
- `2` → Salida (cuadrado pequeño marcado dentro de la celda)

Métodos:
- `new()` — constructor, inicializa el array de 512 celdas.
- `getCell(row, col)` — retorna el valor de una celda.
- `setCell(row, col, value)` — asigna un valor a una celda.
- `canMove(row, col)` — retorna `true` si la celda no es muro (valor ≠ 1).
- `loadMap()` — inicializa el mapa con las coordenadas de muros y salida (código exportado desde el generador de laberintos).
- `draw()` — recorre todas las celdas y las dibuja en pantalla según su valor.
- `dispose()` — libera la memoria del array y del objeto.

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

## Clases Pendientes de Implementar

### `Player.jack` *(por implementar)*
Representará al jugador dentro del laberinto.

Responsabilidades previstas:
- Almacenar la posición actual en términos de fila y columna (`row`, `col`).
- Leer input de teclado en cada ciclo de juego.
- Consultar `canMove(row, col)` del Maze actual antes de moverse.
- Dibujarse en pantalla (borrar posición anterior, pintar nueva posición).
- Detectar si la celda destino es la salida (`valor 2`) para notificar a `Main` que el nivel terminó.

### `Enemy.jack` *(por implementar)*
Representará a uno o más enemigos que patrullan el laberinto.

Responsabilidades previstas:
- Almacenar posición y dirección de movimiento.
- Implementar lógica de movimiento automático (patrullaje por pasillo, rebote en muros).
- Detectar colisión con el jugador para terminar la partida.
- Dibujarse y borrarse cada turno/ciclo.

---

##  Arquitectura General

```
Main
 ├── Start          (pantalla de inicio)
 ├── Maze           (nivel 1)
 │    ├── Player    (se mueve sobre Maze)
 │    └── Enemy     (patrulla sobre Maze)
 ├── Maze2          (nivel 2)
 │    ├── Player    (reutilizado)
 │    └── Enemy     (puede ser más agresivo)
 └── End            (pantalla de fin)
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
| Almacenamiento del mapa | Array 1D con índice `row * cols + col` |

---

## Autores

Proyecto desarrollado como parte del curso **nand2tetris**
Andres David Osorio Moreno
Daniel Mauricio Giraldo Moreno
