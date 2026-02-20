# 🧟‍♂️ PVZ WEB CLONE - MASTER TASK LIST PARA AGENTES IA 🌻

## 📍 TABLA DE REFERENCIA ESPACIAL (GROUND TRUTH)

* **Cortacésped**: Posición `X = -70`.
* **Escalas (Scale)**:
    * Girasol y Nuez: `0.3`.
    * Lanzaguisantes y Zombis: `0.35`.
    * Soles: `0.5`.
* **Combate**:
    * FPS: `30`.
    * Altura Guisante: `+40px` respecto al centro de su casilla.
    * Fogonazo de disparo: Fotograma `27` del Lanzaguisantes.

---

## 📜 REGLAS GLOBALES DE OPERACIÓN (LEER ANTES DE CODIFICAR)
1. **Un solo archivo:** Todo el desarrollo se hace en `index.html`. NO crees archivos `.js` o `.css`.
2. **Inyección por Anclas:** Busca en `index.html` los comentarios de anclaje (ej. `// --- [INICIO_ZONA_CLASE_SUN_1.1] ---`). Escribe tu código ESTRICTAMENTE dentro de esa zona para evitar Merge Conflicts.
3. **Vanilla JS:** Usa ES6+, HTML5 Canvas Context 2D.
4. **Dummy Trigger Obligatorio:** En cada tarea, debes incluir código de prueba temporal. **MUY IMPORTANTE:** El código de prueba que vaya en `initGame()` o `updateGame()` DEBE ir inyectado estrictamente dentro de tu respectiva "Caja de Arena" (ej. `// === [TRIGGER_TAREA_1.1] ===`). No lo pongas suelto.
5. **Rendimiento:** Animaciones gestionadas usando la variable global `frames` y módulo `%`.
6. **Ramas Estrictas:** Trabaja exclusivamente en la rama indicada usando `git checkout -b <nombre-de-la-rama>`.
7. **USO ESTRICTO DE RECURSOS (SPRITES Y AUDIO):** Tienes PROHIBIDO usar formas primitivas de Canvas (rectángulos, círculos) para representar entidades. Las imágenes ya están precargadas en el objeto global `images` (ej. `ctx.drawImage(images.sol, x, y)` o `images.semillaGirasol`). Debes usar estos *sprites* obligatoriamente. Para los sonidos, instancia y reproduce directamente desde la ruta (ej. `new Audio('recursos/sonidos/Recoger sol 1.wav').play()`).

---

## 📦 LOTE 1: ECONOMÍA, UI Y PLANTADO

### TAREA 1.1: Mecánica de Soles (Caída y Animación)
* **Rama:** `feat/tarea-1.1-mecanica-soles`
* **Objetivo:** Lógica de recolección de moneda (Clase `Sun`).
* **Especificaciones:**
  - `Sun` cae hasta un `finalY` aleatorio y detiene su caída.
  - Al hacer clic, `isCollecting = true`. Usar Lerp (factor `0.15`) para moverlo hacia la UI en `(105, 100)`.
  - Al llegar a destino: sumar 25 soles, reproducir `new Audio('recursos/sonidos/Recoger sol 1.wav').play()`, y crear `FloatingText` ("+25") dorado y partículas (`#FFD700`, `#FFFF00`) tipo `spark` y `circle`.
* **Dummy Trigger (Caja 1.1):** En `initGame()`, invoca `spawnSun(200, 300, false)` y `spawnSun(400, 300, false)` para tener dos soles estáticos listos.

### TAREA 1.2: Planta Fantasma y Selección
* **Rama:** `feat/tarea-1.2-seleccion-fantasma`
* **Objetivo:** Interacción de la barra superior.
* **Especificaciones:**
  - Detectar clic en rectángulos de la UI de semillas (Y entre 20 y 160) para actualizar `selectedSeed`.
  - En `drawGame()`, si hay `selectedSeed` y el ratón está sobre el grid, dibujar la planta con `globalAlpha = 0.4` haciendo *snap* exacto en el centro de la celda.

### TAREA 1.3: Sistema de Plantado y Recursos
* **Rama:** `feat/tarea-1.3-plantado-base`
* **Objetivo:** Instanciar la planta real en el mapa (Clase `Plant`).
* **Especificaciones:**
  - Evento `mousedown`: si hay soles suficientes y cooldown (`timer`) <= 0, descontar costo e instanciar `Plant`.
  - Al plantar: Generar `FloatingText` rojo ("-" + costo), reproducir sonido de plantar, y generar partículas de tierra (`#8B4513`, `#A0522D`).
  - **Daño visual:** La clase `Plant` debe tener un método `takeDamage(amount)` y un `flashTimer`. Al recibir daño, la planta debe parpadear en blanco 4 frames (usando `source-atop` con `rgba(255, 255, 255, 0.3)`).
* **Dummy Trigger (Caja 1.3):** Ajusta temporalmente `sunAmount = 5000` en `initGame()` para tener soles ilimitados.

---

## 📦 LOTE 2: AMENAZA Y COMBATE

### TAREA 2.1: Zombis Base y Feedback de Daño
* **Rama:** `feat/tarea-2.1-zombis-base`
* **Objetivo:** Movimiento, estados y daño del enemigo (Clase `Zombie`).
* **Especificaciones:**
  - Estados: `walk`, `eat`, `die`.
  - Cuando el zombi esté en estado `eat`, debe llamar a `planta.takeDamage(...)` cada ciertos frames.
  - Daño visual: Al recibir daño, usar `flashTimer = 4` para tintar al zombi de blanco (`source-atop` con `rgba(255, 255, 255, 0.3)`).
  - Muerte visual: Cambiar a estado `die`, detener animación al último frame, e iniciar `fadeTimer` hasta 45 para desvanecer su `globalAlpha` antes de marcar para borrado. Generar partículas moradas (`#663399`, `#4B0082`).
* **Dummy Trigger (Caja 2.1):** En `initGame()`, haz `zombies.push(new Zombie())` para forzar su aparición inicial.

### TAREA 2.2: Detección y Fogonazos de Lanzaguisantes
* **Rama:** `feat/tarea-2.2-proyectiles-lanzaguisantes`
* **Objetivo:** Lógica de ataque de la planta y clase `Pea`.
* **Especificaciones:**
  - Si hay un zombi en la misma fila (`row`) y a la derecha (`x > this.x`), cambiar animación a `lg_ataque`.
  - En el `frame === 27` de su animación: llamar a `spawnPea()` y generar partículas de chispas amarillas (`#FFFF00`, `#ADFF2F`) en su boca.
* **Dummy Trigger (Caja 2.2):** En `initGame()`, fuerza la creación de un `new Plant('lanzaguisantes', 0, 2)` y un `new Zombie()` asignado a la misma fila (`row = 2`).

### TAREA 2.3: Colisiones
* **Rama:** `feat/tarea-2.3-colisiones`
* **Objetivo:** Cruce de Hitboxes (`checkCollisions()`).
* **Especificaciones:**
  - Si `Pea` choca con `Zombie` (margen de 40px), llamar `z.takeDamage(20)`, borrar guisante, reproducir sonido de hit y generar partículas verdes (`#33FF33`, `#00CC00`).

---

## 📦 LOTE 3: HERRAMIENTAS Y BALANCE

### TAREA 3.1: Cooldown de Semillas y Pala
* **Rama:** `feat/tarea-3.1-cooldown-pala`
* **Objetivo:** Control de economía.
* **Especificaciones:**
  - Semillas en UI: Dibujar un rectángulo superpuesto oscuro (`rgba(0, 0, 0, 0.5)`) basado en la proporción `timer / cooldown`.
  - Pala (`isShovelActive`): Al hacer clic en una planta existente, marcar para borrado, reproducir sonido de pala y generar partículas marrones de tierra.

### TAREA 3.2: Herramientas de Desarrollo (Debug Mode)
* **Rama:** `feat/tarea-3.2-debug-tools`
* **Objetivo:** Controles de Tech Lead.
* **Especificaciones:**
  - Tecla `P`: Alternar `isPaused`.
  - Tecla `D`: Alternar `isDebug`. Dibuja líneas rojas para el grid, hitboxes rojos para entidades, azules para cortacésped, amarillos para soles/guisantes y texto de FPS en la esquina. En modo Debug, ignorar costos y cooldowns.
* **Dummy Trigger (Caja 3.2):** Forzar `isDebug = true` al inicio para revisar las cajas de colisión.

### TAREA 3.3: Progreso HUD y Audios Aleatorios
* **Rama:** `feat/tarea-3.3-hud-audios`
* **Objetivo:** Feedback visual de hordas e inmersión.
* **Especificaciones:**
  - Crear `drawProgressBar()`. Mover la bandera y la cabeza del zombi en el eje X basándose en `zombiesSpawned / LEVEL_CONFIG.totalZombies`.
  - Bucle de Zombis: Cada 180 frames, si hay zombis vivos, 70% de probabilidad de reproducir un gruñido aleatorio (`groan1` a `groan5`).
* **Dummy Trigger (Caja 3.3):** En `updateGame()`, suma `1` a `zombiesSpawned` cada `30` frames para simular avance rápido.

---

## 📦 LOTE 4: PULIDO VISUAL, UI Y HERRAMIENTAS DE DEPURACIÓN

### TAREA 4.1: Ciclo de Vida de los Soles, Spawner y Feedback
* **Rama:** `feat/tarea-4.1-pulido-soles`
* **Objetivo:** Aparición automática, eliminación y feedback táctil de la moneda.
* **Especificaciones:**
  - **Desaparición por Animación:** La eliminación del sol NO debe depender de su tiempo de inactividad tras la caída. Debe vincularse a su ciclo de animación. Una vez que el objeto `Sun` alcance el último fotograma de su ciclo de animación completo, debe marcarse automáticamente con `isDead = true` para eliminarse de la memoria y del canvas.
  - **Spawner:** En `updateGame()`, instanciar automáticamente un nuevo objeto `Sun` cada 300 frames. Su `X` debe ser aleatoria dentro de los límites del jardín, su `Y` inicial en `-50` (fuera de la pantalla por arriba) y su `finalY` aleatorio dentro del pasto.
  - **Partículas al Clic:** Al hacer clic en un sol para recogerlo, generar un efecto rápido de partículas (ej. chispas amarillas y blancas) en su posición exacta antes de que inicie su viaje hacia la UI.
* **Dummy Trigger (Caja 4.1):** Crea un sol después de un segundo de iniciado el juego para verificar la caída como lluvia y que efectivamente desaparezcan con suavidad al terminar su animación.

### TAREA 4.2: UI de Semillas (Estados, Recarga y Resplandor)
* **Rama:** `feat/tarea-4.2-ui-semillas`
* **Objetivo:** Mejorar la comunicación visual de la barra de semillas.
* **Especificaciones:**
  - **Efecto de Carga:** El rectángulo oscuro de cooldown (`rgba(0, 0, 0, 0.5)`) debe revelar la semilla de abajo hacia arriba a medida que `timer` se acerca a 0 (antes se realizaba de arriba hacia abajo, hay que invertir ese visual).
  - **Falta de Soles:** Si la planta está lista (`timer <= 0`) pero el jugador NO tiene los soles suficientes, la imagen de la semilla en la UI debe renderizarse semitransparente (ej. `globalAlpha = 0.5`).
  - **Resplandor Sutil:** Si la planta está lista Y hay soles suficientes, aplicar un resplandor (`ctx.shadowColor = 'rgba(255, 255, 200, 0.7)'`, `ctx.shadowBlur = 15`) a su ranura antes de dibujarla, reseteando el blur a `0` inmediatamente después.
  - **Override de Depuración:** Si `isDebug === true`, se deben ignorar visual y lógicamente los costos y cooldowns. Las semillas siempre deben verse opacas, sin rectángulo oscuro, y permitir el plantado libre.

### TAREA 4.3: Modo Depuración Avanzado y Pantalla de Pausa
* **Rama:** `feat/tarea-4.3-debug-avanzado`
* **Objetivo:** Controles de Tech Lead, manipulación de entidades y estado de pausa.
* **Especificaciones:**
  - **Pantalla de Pausa:** Si `isPaused === true`, dibujar un rectángulo negro semitransparente (`rgba(0,0,0,0.5)`) que cubra todo el canvas, y un texto centrado que diga "PAUSADO" en fuente grande y blanca.
  - **Visualización de Hitboxes (SOLO VISUAL):** NO MODIFICAR las propiedades físicas ni la lógica de colisiones actual. Únicamente, cuando `isDebug === true`, ajustar los offsets (`x`, `y`, `width`, `height`) en el `ctx.strokeRect` al dibujar las cajas rojas de Zombis y Plantas para que el trazo calce mejor con el cuerpo del sprite, ignorando el espacio vacío de la imagen.
  - **Edición de Soles en Debug:** En modo depuración, permitir modificar la cantidad de soles (ej. detectando un clic sobre el contador de soles en la UI para abrir un `prompt()` que pida el nuevo valor).
  - **Arrastre de Zombis (Drag & Drop):** Si `isDebug === true`, permitir hacer clic sostenido sobre un zombi para arrastrarlo (actualizando su `X` e `Y` con el ratón) y soltarlo en otra posición del grid.

  ## 📦 LOTE 5: EL CAOS Y EL FINAL

### TAREA 5.1: La Petacereza, Screen Shake y Limpieza
* **Rama:** `feat/tarea-5.1-petacereza`
* **Objetivo:** Explosivo de área con destrucción precisa y vulnerabilidad.
* **Especificaciones:**
  - **Vulnerabilidad:** La clase `Plant` tipo `cereza` debe heredar la función `takeDamage`. Los zombis pueden comérsela mientras se infla (frames 1 al 22).
  - **Explosión y AoE:** En el `frame === 23`, invoca `screenShake = 15`, reproduce la explosión y 80 partículas de fuego (`#FF4500`, `#FF8C00`). El daño masivo (9999) no debe ser un radio circular, sino que debe afectar a los zombis que estén en una cuadrícula de 3x3 (la misma fila `row` y las adyacentes `row - 1` y `row + 1`, y en un rango de `X` cercano).
  - **Autodestrucción:** Inmediatamente después de aplicar el daño en el frame 23, la planta DEBE marcarse con `isDead = true` para eliminarse del grid. Zombis afectados cambian a estado `charred` (`source-in` negro) y se desvanecen.

### TAREA 5.2: Director de Hordas en Columnas y Cortacésped
* **Rama:** `feat/tarea-5.2-hordas-cortacesped`
* **Objetivo:** Spawner avanzado y herramienta de defensa final.
* **Especificaciones:**
  - **Hordas en Columnas:** Al llegar a `horde1Threshold` o `horde2Threshold`, mostrar "¡GRAN HORDA DE ZOMBIS!". Pausar el spawn regular durante 240 frames (`hordeDelayTimer`). Luego, instanciar los zombis de la horda en **columnas verticales**: asígnales la misma posición `X` (ej. `canvas.width + 50` y `canvas.width + 120`) pero distribuidos en diferentes filas (`row` 0 a 4).
  - **Cortacésped (GC):** En `initGame()`, instanciar 5 objetos `Lawnmower`, uno por cada fila en `X = -70`. Al chocar con un zombi, activar sonido, avanzar rápido y causar daño 9999 (chispas metálicas). **Regla de Limpieza:** Si `Lawnmower` supera `canvas.width`, marcarlo con `isDead = true`.

### TAREA 5.3: Balance PvZ, Zombis Orgánicos y Fin de Partida
* **Rama:** `feat/tarea-5.3-balance-fin-partida`
* **Objetivo:** Ajustes matemáticos de PvZ 1, desincronización y estados de juego.
* **Especificaciones:**
  - **Balance de Entidades (Ground Truth):**
    - HP Zombi Básico: `200` (10 guisantes de 20 daño).
    - HP Planta Base: `300`.
    - HP Nuez: `4000`.
    - Daño Zombi: Restar `50` HP a la planta cada `15` frames en estado `eat`.
  - **Zombis Orgánicos (Desincronización):** En el constructor de la clase `Zombie`, añade un factor aleatorio sutil a su velocidad de movimiento (ej. `this.vx = baseSpeed + (Math.random() * 0.1 - 0.05)`) y a la velocidad en la que cambian sus frames de animación (`frameDelay`). Esto evitará que varios zombis en la misma casilla se muevan y muerdan exactamente al mismo tiempo.
  - **Condición de Derrota:** En `updateGame()`, si la posición `X` de un zombi es `< -50` y ya no hay cortacésped en su fila, aplicar `isPaused = true` y mostrar texto: "¡LOS ZOMBIS SE COMIERON TUS CEREBROS!".
  - **Condición de Victoria:** Si `pendingZombies.length === 0`, no hay zombis vivos en el array principal y se completaron las hordas, aplicar `isPaused = true` y mostrar: "¡NIVEL COMPLETADO!".