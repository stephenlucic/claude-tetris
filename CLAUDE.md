# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Tetris en JavaScript vanilla + HTML5 Canvas. Sin dependencias, sin `package.json`, sin build, sin lint, sin tests. README.md está en español (documentación y comentarios del proyecto en español).

## Ejecutar

```bash
open index.html              # directo
python3 -m http.server 8000  # o servidor estático, luego http://localhost:8000
```

No hay comando de test. Verificar cambios jugando en el navegador.

## Arquitectura

Tres archivos: `index.html` (DOM + `<canvas id="board">` 300×600 + `<canvas id="next-canvas">` 120×120 + overlay), `style.css` (tema oscuro), `game.js` (toda la lógica, script global sin módulos, cargado al final del body).

`game.js` — estado global mutable (`board, current, next, score, lines, level, paused, gameOver, dropInterval...`) declarado en una sola línea `let`; funciones top-level:
- Flujo: `init()` → `spawn()` → `loop(ts)` (rAF, acumula `dropAccum` vs `dropInterval`) → `lockPiece()` (`merge` + `clearLines` + `spawn`). `spawn()` que colisiona dispara `endGame()`.
- Rotación: `rotateCW` (transpuesta + reverso) y `tryRotate` (wall kicks ±1/±2 columnas).
- Render: `draw()` = `drawGrid` + tablero + ghost (`ghostY`, alpha 0.2) + pieza actual; `drawNext()` para preview.
- Velocidad: `max(100, 1000 − (level−1)×90)` ms; nivel sube cada 10 líneas. Puntos: `LINE_SCORES × level`, hard drop 2/celda, soft drop 1/fila.

## Gotchas

- Si cambias `COLS`, `ROWS` o `BLOCK` en `game.js`, actualiza `width`/`height` del canvas `#board` en `index.html` (`COLS×BLOCK` × `ROWS×BLOCK`); no se derivan automáticamente.
- IDs del DOM (`score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`) se obtienen por `getElementById` en `game.js`; renombrarlos en HTML rompe el juego.
