# 🧟‍♂️ PVZ WEB CLONE - MASTER TASK LIST PARA AGENTES IA 🌻

## 📜 REGLAS GLOBALES DE OPERACIÓN (LEER ANTES DE CODIFICAR)
1. **Un solo archivo:** Todo el desarrollo se hace en `index.html`. NO crees archivos `.js` o `.css` separados.
2. **Inyección por Anclas:** Busca en `index.html` los comentarios de anclaje (ej. `// --- [INICIO TAREA 1.1] ---`). Escribe tu código ESTRICTAMENTE dentro de esa zona para evitar Merge Conflicts con otros agentes trabajando en paralelo.
3. **Vanilla JS:** Usa ES6+, HTML5 Canvas Context 2D. Prohibido usar librerías externas o módulos.
4. **Dummy Trigger Obligatorio:** En cada tarea, debes incluir un fragmento de código temporal (marcado con `// DUMMY TRIGGER PARA TESTING`) que fuerce la visualización de tu código. El Tech Lead revisará tu PR visualmente en el navegador. Si tu código no se puede ver/escuchar inmediatamente al abrir la página, el PR será rechazado.
5. **Rendimiento:** Las animaciones lógicas van a 30 FPS gestionadas mediante contadores de frames (usando la variable global `frames` y modulo `%`).

---

## 📦 LOTE 1: SISTEMAS BASE Y ENTORNO (Ejecución Paralela Segura)

### TAREA 1.1: Motor de Audio (`AudioManager`)
* **Objetivo:** Crear la clase para gestionar los sonidos del juego precargándolos desde la carpeta `recursos/sonidos/`.
* **Especificaciones:** - Usar `encodeURIComponent` para las rutas (hay archivos con espacios, ej: `Los zombis se acercan inicio.mp3`).
  - El método `play(name, volume)` debe hacer un `.cloneNode()` del audio para permitir que el mismo sonido se reproduzca varias veces simultáneamente (ej. múltiples choques de guisantes).
* **Dummy Trigger:** Añade un `window.addEventListener('click')` global que reproduzca `Los zombis se acercan inicio.mp3` la primera vez que el usuario haga clic en la pantalla.

### TAREA 1.2: Sistema de Efectos Visuales (Partículas y Textos)
* **Objetivo:** Crear las clases `Particle` y `FloatingText`, y la función global `spawnParticles(x,y,colors,count,speed,size,types)`.
* **Especificaciones:**
  - Las partículas deben tener física: velocidad inicial radial (sin/cos), gravedad (`vy += 0.2`) y fricción del aire (`vx *= 0.92; vy *= 0.92`).
  - Soportar 3 tipos de renderizado en Canvas: `'circle'`, `'square'`, y `'spark'` (este último usa `globalCompositeOperation = 'lighter'` para efecto de brillo).
  - Los textos flotantes deben subir lentamente en el eje Y y desvanecerse (fade-out con `globalAlpha`).
* **Dummy Trigger:** Un evento `mousedown` en el Canvas que llame a `spawnParticles` generando una explosión multicolor y un `FloatingText` que diga "TEST" en la posición del ratón.

### TAREA 1.3: Entidades Pasivas (`Sun`, `Pea`, `Lawnmower`)
* **Objetivo:** Crear la lógica de movimiento base de entidades que no requieren IA compleja.
* **Especificaciones:**
  - `Sun`: Debe caer en el eje Y hasta un punto aleatorio y detenerse. Debe tener un método `collect()` vacío (se implementará en el Lote 2).
  - `Pea`: Proyectil que viaja constantemente hacia la derecha (eje X).
  - `Lawnmower`: Se desplaza hacia la derecha a alta velocidad cuando `active == true`.
* **Dummy Trigger:** En el bucle principal (`updateGame`), instancia automáticamente un `Sun` cayendo y un `Pea` cruzando la pantalla cada 120 frames.

---

## 📦 LOTE 2: INTERFAZ (UI) Y CONTROLES (Ejecución Paralela Segura)

### TAREA 2.1: Interacción de Economía (Soles)
* **Objetivo:** Permitir la recolección de soles y actualizar el HUD.
* **Especificaciones:**
  - Detectar colisión del ratón (clic) con el radio de los objetos `Sun` activos.
  - Al hacer clic, el sol debe moverse usando interpolación matemática (Lerp) hacia el contador de la esquina superior izquierda `(105, 100)`.
  - Al llegar: sumar 25 a `sunAmount`, reproducir sonido, borrar el sol e instanciar un `FloatingText` dorado con "+25".
* **Dummy Trigger:** Instanciar 3 soles estáticos en el suelo al iniciar el juego para que el Tech Lead pueda hacer clic en ellos inmediatamente.

### TAREA 2.2: Selección de Semillas y Planta Fantasma
* **Objetivo:** Lógica del menú superior y visualización en la cuadrícula.
* **Especificaciones:**
  - Detectar clics en las "cartas" de la barra superior para guardar el objeto en `selectedSeed`.
  - En `drawGame()`, si hay una `selectedSeed`, detectar sobre qué celda de la cuadrícula (9 columnas x 5 filas, de 90,280 a 1577,1260) está el ratón.
  - Dibujar la imagen de la planta correspondiente haciendo *snap* exacto en el centro de esa celda, con un `globalAlpha = 0.4` (Planta Fantasma). Si la celda está ocupada (`gridState !== null`), no dibujar la planta fantasma.
* **Dummy Trigger:** Ninguno extra, la propia planta fantasma al pasar el ratón es el trigger.

### TAREA 2.3: Barra de Progreso HUD
* **Objetivo:** Renderizar el indicador de oleadas en la esquina inferior derecha.
* **Especificaciones:**
  - Función `drawProgressBar()`. Ensamblar `barra.png`, `bandera.png` y `cabeza de zombi.png`.
  - El relleno verde de progreso y la cabeza del zombi deben avanzar de DERECHA a IZQUIERDA basado en la fórmula `zombiesSpawned / LEVEL_CONFIG.totalZombies`.
* **Dummy Trigger:** Forzar que la variable `zombiesSpawned` aumente un punto cada 10 frames de forma automática y se reinicie al llegar al máximo, para ver la barra moverse de derecha a izquierda infinitamente.

---

## 📦 LOTE 3: EL REPARTO PRINCIPAL (Ejecución Paralela Segura)

### TAREA 3.1: Lógica de las Plantas (`Plant`)
* **Objetivo:** Comportamientos y animaciones de los 4 tipos de plantas.
* **Especificaciones:**
  - **Girasol:** Genera un `Sun` en sus coordenadas cada X frames.
  - **Lanzaguisantes:** En el frame 27 de su animación de ataque, instancia un `Pea` e invoca `spawnParticles` (chispas amarillas simulando fogonazo).
  - **Cereza:** En su fotograma 23, explota (daño radial masivo a zombis), sacude la pantalla (`screenShake`) y genera humo/fuego masivo con `spawnParticles`.
  - **Sombra:** Todas las plantas deben dibujar una sombra dinámica elíptica en su base basada en su ancho/alto.
* **Dummy Trigger:** En la función `initGame()`, instanciar (hardcodear) un Girasol, un Lanzaguisantes y una Cereza en diferentes casillas para ver sus bucles de animación al abrir la web.

### TAREA 3.2: Máquina de Estados del Zombi (`Zombie`)
* **Objetivo:** Movimiento, animaciones y muerte de los enemigos.
* **Especificaciones:**
  - Estados: `walk`, `eat`, `die`, `charred` (quemado por cereza).
  - Fade-out: Cuando el estado sea `die` y termine su animación, el sprite debe reducir su opacidad progresivamente hasta desaparecer.
  - Sombra: Dibujar una elipse oscura bajo el zombi que se desvanezca al mismo ritmo que su fade-out.
* **Dummy Trigger:** Instanciar automáticamente un Zombi cada 60 frames apareciendo desde el lado derecho extremo de la pantalla caminando hacia la izquierda.

---

## 📦 LOTE 4: COLISIONES Y COMBATE (¡CUIDADO AL HACER MERGE!)
*Nota para los Agentes: Ambas tareas modifican el ciclo `updateGame()` y métodos de las entidades. Aísla bien tu código.*

### TAREA 4.1: Plantas atacan Zombis
* **Objetivo:** Registro de impactos de proyectiles y área de efecto.
* **Especificaciones:**
  - En `checkCollisions()`, si un `Pea` toca el hitbox de un Zombi: borrar guisante, reproducir sonido hit, crear partículas de savia verde, restar 20 HP al zombi.
  - Tintado de Daño: El zombi debe parpadear en blanco durante 4 frames (usando un canvas off-screen y `globalCompositeOperation = 'source-atop'`).
  - Al morir (`HP <= 0`), pasar a estado `die` y soltar polvo morado.
* **Dummy Trigger:** Un zombi inmóvil en el centro del mapa que reciba impactos automáticos de guisantes cada segundo.

### TAREA 4.2: Zombis comen y Cortacésped
* **Objetivo:** Daño a las plantas y defensa final.
* **Especificaciones:**
  - Si el Zombi choca con una Planta: detener movimiento, pasar a estado `eat`, restar HP a la planta. La planta debe parpadear en rojo (`source-atop`).
  - Si la planta muere, limpiar el `gridState` y el Zombi vuelve a `walk`.
  - Si el zombi toca el radio de activación de un `Lawnmower`, el cortacésped se activa, viaja a la derecha, mata al zombi instantáneamente soltando chispas metálicas.
* **Dummy Trigger:** Una fila con un zombi caminando, una nuez en medio de su camino, y un cortacésped en el borde izquierdo.

---

## 📦 LOTE 5: EL GAME DIRECTOR (Ejecución Paralela Segura)

### TAREA 5.1: Ciclo de Plantación Real
* **Objetivo:** Integrar la UI del ratón con la colocación definitiva en el mapa.
* **Especificaciones:**
  - Evento `mousedown`: Si hay `selectedSeed` y la celda está libre (`gridState == null`), verificar si hay suficientes soles (`sunAmount >= cost`).
  - Si es válido: descontar costo, instanciar `Plant`, bloquear `gridState`, generar partículas de tierra marrón y un `FloatingText` rojo indicando el gasto (ej. "-50").
* **Dummy Trigger:** Ninguno, el flujo natural de juego debe funcionar (plantar gastando recursos).

### TAREA 5.2: Sistema de Hordas y Timings (Eventos basados en Frames)
* **Objetivo:** Controlar el flujo de la partida y los eventos guionizados.
* **Especificaciones:**
  - Usar un array `pendingZombies` como cola de espera.
  - Al llegar a `horde1Threshold` (ej. 20 zombis) o `horde2Threshold` (ej. 38 zombis):
    1. Pausar el spawn normal temporalmente.
    2. Mostrar texto "¡GRAN HORDA DE ZOMBIS!" en pantalla grande, centrado y parpadeando en rojo.
    3. Reproducir audio de horda.
    4. Esperar exactamente 240 frames (4 segundos).
    5. Inyectar múltiples zombis en el array `pendingZombies` para que aparezcan de golpe.
  - La música de fondo (BGM) debe iniciar automáticamente 60 frames (1 seg) antes del primer zombi.
* **Dummy Trigger:** Ajustar temporalmente los umbrales para que el nivel total tenga 5 zombis y la primera horda ocurra al generar el zombi número 2, así el evento de "Gran Horda" se dispara a los pocos segundos de testear.