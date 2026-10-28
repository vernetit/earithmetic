# Cálculo mental · Notes & Rhythm

**[English](#english) · [Español](#español)**

<p align="center">
  <img src="docs/home.png" width="230" alt="Home screen">
  <img src="docs/game-rhythm.png" width="230" alt="Game in rhythm mode">
  <img src="docs/game-notes-feedback.png" width="230" alt="Game in notes mode, after a mistake">
</p>

---

<a id="english"></a>
## English

### What it is

**Cálculo mental** trains **mental multiplication by ear**. The numbers are never shown while you work: **every digit is heard**, and you have to hold the problem in your head, multiply, and type the result.

There are two sound modes:

| Mode | Each digit is… | Reference |
|---|---|---|
| **♪ Notes** | a musical note | a reference note (A3 = 220 Hz by default) |
| **🥁 Rhythm** | a rhythm that lasts one quarter-note beat | a count-in of clicks |

You can still see **where** each digit is: white squares show the shape of the problem, and the answer is typed on a white keyboard without labels. The real numbers only appear after checking.

- **One HTML file** (~90 KB). No libraries, no internet, no install. Open it in a browser and play.
- **Mobile first**: designed for Chrome on Android, played with your thumbs. The game screen fills the phone screen exactly.
- **Interface in Spanish (default) and English.**
- **Sound is synthesized live** with the Web Audio API. There are no audio files.
- **All data stays in your browser** (`localStorage`). You can export and import it as JSON.

### How to play

1. On the home screen, choose **♪ Notes** or **🥁 Rhythm**.
2. Tap **▶ Play** to use the adaptive level, or choose a **fixed level** (1×1 up to 8×8 digits).
3. **Listen** to the problem in this order:
   - the reference (the reference note, or the count-in clicks);
   - the digits of the top number;
   - a pause;
   - the digits of the bottom number.
4. Work it out and type the answer **from right to left**, starting with the units, the way you write a multiplication on paper.
5. The answer is checked automatically when all boxes are filled. If the result has fewer digits, press **⏎**.
6. After checking, the numbers are revealed. If you were wrong, your answer (**you**) is shown above the right one (**right**), with the differing digits marked in red. Every box can be tapped to hear its digit.

To hear the problem again, use **▶** in the top bar or the **▶** right above the **⌫** key.

The **? Help** button on the home screen explains all of this inside the app, with the tables of your current notes and rhythms.

### The game screen

The game screen has four levels, from top to bottom:

| Level | What it is | What it does |
|---|---|---|
| **1 · Reference row** | 11 keys: the reference + digits 0–9, with the note name (♪) or the drawn rhythm (🥁) | Tap to hear and compare as many times as you want |
| **2 · White squares** | The shape of the problem (top number, ×, bottom number) | Not tappable; each square lights up while its digit sounds |
| **3 · Answer boxes ♪** | Your answer, filled right to left | Tap a box to hear what you typed |
| **4 · Pad + keyboard** | The problem as blank buttons, ▶ above ⌫, and the white keyboard `0 1 2 3 4 ⌫ / 5 6 7 8 9 ⏎` | Each pad button plays its digit; ▶ replays the whole problem |

### The note code (♪ mode)

The default code is below. You can change every note in **Settings → Notes per digit**.

| Digit | ref | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Note | A3 | B3 | C4 | C#4 | D4 | D#4 | E4 | F4 | F#4 | G4 | G#4 |
| Interval from ref | — | M2 | m3 | M3 | P4 | A4 | P5 | m6 | M6 | m7 | M7 |

### The rhythm code (🥁 mode)

Each digit lasts one quarter-note beat (60 bpm by default).

| Digit | Default rhythm | Pattern |
|---|---|---|
| 0 | quarter rest, marked by a low dull thud | `-` |
| 1 | quarter note | `x` |
| 2 | 2 eighths | `xx` |
| 3 | eighth triplet | `xxx` |
| 4 | 4 sixteenths | `xxxx` |
| 5 | quintuplet | `xxxxx` |
| 6 | sixteenth sextuplet | `xxxxxx` |
| 7 | septuplet | `xxxxxxx` |
| 8 | 8 thirty-seconds | `xxxxxxxx` |
| 9 | sixteenth triplet + 4 thirty-seconds | `xxx,xxxx` |

These details make the rhythms readable by ear:

- **Accent on the beat**: the first hit of each beat is louder, so `1 1` (two quarters) and `2` (two eighths) sound different.
- **Count-in**: 2 clicks before the problem set the tempo.
- **Pause between the numbers**: 2 silent, unmarked beats, so it can't be confused with a 0.

#### Writing your own rhythms

Each digit's rhythm is a short text in **Settings → Rhythms per digit**. A live drawing and a ▶ preview button sit next to each field.

| Sign | Meaning | Example |
|---|---|---|
| `x` | hit | `x` = quarter note |
| `X` | accented hit | `Xxx` = triplet, first one accented |
| `-` | rest | `x-x` = triplet with the middle note silent |
| (group) | the signs of a group share its time equally | `xxxxx` = quintuplet |
| `,` | splits the beat into equal parts | `xxx,xxxx` = 3 in the first half, 4 in the second |
| space | adds another beat (up to 4) | `x xx` = lasts two quarter notes |

A beat that is entirely silent is always marked with the low thud, like the 0.

### Adaptive level

There are 36 levels, from **1×1 to 8×8 digits**, ordered by the number of partial products. Each level has a target time of `8 + 6·a·b` seconds. The timer starts **when the problem has finished playing**.

| Result | Rating change |
|---|---|
| Right, within the target time | +0.25 |
| Right, up to 2× the target time | +0.08 |
| Right, but slower than that | −0.10 |
| Wrong or skipped | −0.50 |

- When you move up or down a level, you enter it halfway.
- **Each sound mode has its own rating**, so being good with notes doesn't push up your rhythm level.
- Playing a fixed level doesn't change the rating.

### Settings

| Section | What you can change |
|---|---|
| General | Language (ES/EN), sound mode |
| Sound and melody | Volume, **melody tempo (bpm)**, note length, auto-play, reference note before the problem, sound when typing, right/wrong sound |
| Rhythm | **Rhythm tempo (bpm)**, count-in clicks (0–4), accent on the beat, soft click on every beat, play the hits with each digit's note |
| Notes per digit | Reference note and the note of each digit (C2–C6). It warns you if two digits share the same pitch |
| Rhythms per digit | The pattern of each digit, with drawing and preview |
| Visual aids | Show numbers on the pad / on the answer keyboard |
| Game | Auto-check, pause after a right answer, wait for "Next" after a mistake, problems per session |

### Statistics

Statistics are shown **per mode** (♪ / 🥁):

- totals;
- rating evolution chart;
- accuracy by level;
- a table by level (count, % right, median, best, target);
- the last 20 problems.

You can **export** everything to JSON, **import** it (on another phone, for example), or delete it.

### Keyboard (computer)

| Key | Action |
|---|---|
| Digits | Type |
| Backspace | Delete |
| Enter | Check / Next |
| Space | Play the problem |
| Esc | Exit |

### Running it

- **Easiest:** download `dist/calculo-mental.html` and open it in Chrome (on your phone or computer).
- **From the source:** open `index.html`. It also works straight from disk (`file://`), because the scripts are classic scripts, not ES modules.
- **GitHub Pages:** enable Pages on the repository root. `index.html` is the site.

The first tap on the page unlocks audio, as mobile browsers require.

### Project structure

```
index.html          screens: home, game, help, statistics, settings
css/styles.css      styles (mobile first)
js/storage.js       localStorage, defaults, settings table, migration
js/i18n.js          Spanish/English texts, note and interval names
js/rhythm.js        rhythm pattern parser and SVG drawing
js/audio.js         synthesis (notes, hits, thud, clicks), sequencer, mode layer
js/engine.js        problem generator, levels, adaptive rating
js/ui-common.js     helpers
js/ui-game.js       game screen (4 levels)
js/ui-home.js       home screen
js/ui-help.js       help screen
js/ui-stats.js      statistics, export / import
js/ui-settings.js   settings, note and rhythm editors
js/main.js          startup
build.py            builds the single-file version
dist/               single-file version (calculo-mental.html)
docs/               screenshots
TECHNICAL.md        technical description (in Spanish), written so another AI or developer can modify the code
```

The load order matters: storage → i18n → rhythm → audio → engine → ui-common → ui-game → ui-home → ui-help → ui-stats → ui-settings → main.

To rebuild the single file after editing:

```bash
python3 build.py . dist/calculo-mental.html
```

---

<a id="español"></a>
## Español

### Qué es

**Cálculo mental** entrena la **multiplicación mental de oído**. Mientras resolvés no ves los números: **cada cifra se escucha**. Tenés que retener la cuenta en la cabeza, multiplicar y escribir el resultado.

Hay dos modos de sonido:

| Modo | Cada cifra es… | Referencia |
|---|---|---|
| **♪ Notas** | una nota musical | una nota de referencia (La3 = 220 Hz por defecto) |
| **🥁 Ritmo** | un ritmo que dura un pulso de negra | un conteo de clicks |

Lo que sí ves es **dónde** está cada cifra: los cuadrados blancos muestran la forma de la cuenta, y la respuesta se escribe en un teclado blanco sin rótulos. Los números reales recién aparecen al corregir.

- **Un solo archivo HTML** (~90 KB). Sin librerías, sin internet, sin instalar nada. Lo abrís en el navegador y jugás.
- **Pensado para el celular**: Chrome en Android, se juega con los pulgares. La pantalla de juego ocupa justo la pantalla del teléfono.
- **Interfaz en español (por defecto) e inglés.**
- **El sonido se sintetiza en el momento** con la Web Audio API. No hay archivos de audio.
- **Los datos quedan en tu navegador** (`localStorage`). Se pueden exportar e importar en JSON.

### Cómo se juega

1. En la pantalla de inicio elegí **♪ Notas** o **🥁 Ritmo**.
2. Tocá **▶ Jugar** para el nivel adaptativo, o elegí un **nivel fijo** (de 1×1 a 8×8 cifras).
3. **Escuchá** la cuenta, que suena en este orden:
   - la referencia (la nota de referencia, o los clicks de conteo);
   - las cifras del número de arriba;
   - un silencio;
   - las cifras del número de abajo.
4. Hacé la cuenta y escribí el resultado **de derecha a izquierda**, empezando por las unidades, como cuando multiplicás en papel.
5. Se corrige solo cuando llenás todos los casilleros. Si el resultado tiene menos cifras, apretá **⏎**.
6. Al corregir aparecen los números. Si te equivocaste, ves tu respuesta (**vos**) arriba de la correcta (**bien**), con las cifras distintas en rojo. Podés tocar cualquier casillero para escuchar su cifra.

Para volver a escuchar la cuenta usá el **▶** de la barra de arriba o el **▶** que está justo sobre la tecla **⌫**.

El botón **? Ayuda** de la pantalla de inicio explica todo esto dentro de la app, con las tablas de tus notas y ritmos actuales.

### La pantalla de juego

La pantalla de juego tiene cuatro niveles, de arriba hacia abajo:

| Nivel | Qué es | Qué hace |
|---|---|---|
| **1 · Fila de referencia** | 11 teclas: la referencia + cifras 0–9, con el nombre de la nota (♪) o el ritmo dibujado (🥁) | Tocás para escuchar y comparar todas las veces que quieras |
| **2 · Cuadrados blancos** | La forma de la cuenta (número de arriba, ×, número de abajo) | No se tocan; cada cuadrado se ilumina mientras suena su cifra |
| **3 · Casilleros ♪** | Tu respuesta, que se llena de derecha a izquierda | Tocás un casillero para escuchar lo que pusiste |
| **4 · Pad + teclado** | La cuenta en botones vacíos, ▶ sobre ⌫ y el teclado blanco `0 1 2 3 4 ⌫ / 5 6 7 8 9 ⏎` | Cada botón del pad toca su cifra; ▶ repite toda la cuenta |

### Código de notas (modo ♪)

Este es el código por defecto. Cada nota se cambia en **Configuración → Notas por cifra**.

| Cifra | ref | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Nota | La3 | Si3 | Do4 | Do#4 | Re4 | Re#4 | Mi4 | Fa4 | Fa#4 | Sol4 | Sol#4 |
| Intervalo desde la ref | — | 2ª M | 3ª m | 3ª M | 4ª J | 4ª aum | 5ª J | 6ª m | 6ª M | 7ª m | 7ª M |

### Código de ritmos (modo 🥁)

Cada cifra dura un pulso de negra (60 bpm por defecto).

| Cifra | Ritmo por defecto | Patrón |
|---|---|---|
| 0 | silencio de negra, marcado con un golpe grave y sordo | `-` |
| 1 | negra | `x` |
| 2 | 2 corcheas | `xx` |
| 3 | tresillo de corcheas | `xxx` |
| 4 | 4 semicorcheas | `xxxx` |
| 5 | quintillo | `xxxxx` |
| 6 | seisillo de semicorcheas | `xxxxxx` |
| 7 | septillo | `xxxxxxx` |
| 8 | 8 fusas | `xxxxxxxx` |
| 9 | tresillo de semicorcheas + 4 fusas | `xxx,xxxx` |

Estos detalles hacen que los ritmos se distingan de oído:

- **Acento en el pulso**: el primer golpe de cada pulso suena más fuerte, así `1 1` (dos negras) y `2` (dos corcheas) suenan distinto.
- **Conteo**: 2 clicks antes de la cuenta marcan el tempo.
- **Silencio entre los dos números**: 2 pulsos sin marcar, para que no se confunda con un 0.

#### Cómo escribir tus propios ritmos

El ritmo de cada cifra es un texto corto que se edita en **Configuración → Ritmos por cifra**. Al lado de cada campo hay un dibujo que se actualiza mientras escribís y un botón ▶ para escucharlo.

| Signo | Significa | Ejemplo |
|---|---|---|
| `x` | golpe | `x` = negra |
| `X` | golpe con acento | `Xxx` = tresillo con acento en el primero |
| `-` | silencio | `x-x` = tresillo con el del medio en silencio |
| (grupo) | los signos de un grupo se reparten su tiempo en partes iguales | `xxxxx` = quintillo |
| `,` | divide el pulso en partes iguales | `xxx,xxxx` = 3 en la primera mitad, 4 en la segunda |
| espacio | suma otro pulso (hasta 4) | `x xx` = dura dos negras |

Un pulso entero en silencio siempre se marca con el golpe sordo, igual que el 0.

### Nivel adaptativo

Hay 36 niveles, de **1×1 a 8×8 cifras**, ordenados por cantidad de productos parciales. Cada nivel tiene un tiempo objetivo de `8 + 6·a·b` segundos. El cronómetro arranca **cuando termina de sonar la cuenta**.

| Resultado | Cambio del rating |
|---|---|
| Bien, dentro del tiempo objetivo | +0,25 |
| Bien, hasta el doble del tiempo | +0,08 |
| Bien, pero más lento que eso | −0,10 |
| Mal o pasar | −0,50 |

- Cuando subís o bajás de nivel, entrás por la mitad.
- **Cada modo de sonido tiene su propio rating**: ir bien en notas no te sube el nivel de ritmo.
- Jugar un nivel fijo no cambia el rating.

### Configuración

| Sección | Qué se puede cambiar |
|---|---|
| General | Idioma (ES/EN), modo de sonido |
| Sonido y melodía | Volumen, **velocidad de la melodía (bpm)**, duración de cada nota, tocar la cuenta al empezar, nota de referencia antes de la cuenta, sonido al escribir, sonido de acierto/error |
| Ritmo | **Velocidad del ritmo (bpm)**, clicks de conteo (0–4), acento en el pulso, click suave en cada pulso, tocar los golpes con la nota de cada cifra |
| Notas por cifra | Nota de referencia y nota de cada cifra (Do2–Do6). Avisa si dos cifras suenan igual |
| Ritmos por cifra | El patrón de cada cifra, con dibujo y vista previa |
| Ayudas visuales | Mostrar números en el pad / en el teclado de respuesta |
| Juego | Corregir solo, pausa después de un acierto, esperar "Seguir" tras un error, problemas por sesión |

### Estadísticas

Las estadísticas se ven **por modo** (♪ / 🥁):

- totales;
- gráfico de evolución del rating;
- aciertos por nivel;
- tabla por nivel (cantidad, % bien, mediana, mejor, objetivo);
- las últimas 20 cuentas.

Podés **exportar** todo a JSON, **importarlo** (por ejemplo, en otro celular) o borrarlo.

### Teclado de la compu

| Tecla | Acción |
|---|---|
| Números | Escribir |
| Retroceso | Borrar |
| Enter | Corregir / Seguir |
| Espacio | Tocar la cuenta |
| Esc | Salir |

### Cómo usarlo

- **Lo más simple:** bajá `dist/calculo-mental.html` y abrilo en Chrome, en el celular o en la compu.
- **Desde el código:** abrí `index.html`. También funciona directo desde el disco (`file://`), porque los scripts son clásicos y no módulos ES.
- **GitHub Pages:** activá Pages en la raíz del repositorio. `index.html` es el sitio.

El primer toque en la página habilita el audio, porque los navegadores de celular lo exigen.

### Estructura del proyecto

```
index.html          pantallas: inicio, juego, ayuda, estadísticas, configuración
css/styles.css      estilos (pensados primero para celular)
js/storage.js       localStorage, valores por defecto, tabla de configuración, migración
js/i18n.js          textos en español e inglés, nombres de notas e intervalos
js/rhythm.js        lectura de patrones de ritmo y dibujo SVG
js/audio.js         síntesis (notas, golpes, golpe sordo, clicks), secuenciador, capa de modo
js/engine.js        generador de cuentas, niveles, rating adaptativo
js/ui-common.js     utilidades
js/ui-game.js       pantalla de juego (4 niveles)
js/ui-home.js       pantalla de inicio
js/ui-help.js       pantalla de ayuda
js/ui-stats.js      estadísticas, exportar / importar
js/ui-settings.js   configuración, editores de notas y ritmos
js/main.js          arranque
build.py            arma la versión de un solo archivo
dist/               versión de un solo archivo (calculo-mental.html)
docs/               capturas
TECHNICAL.md        descripción técnica, pensada para que otra IA o un programador pueda modificar el código
```

El orden de carga importa: storage → i18n → rhythm → audio → engine → ui-common → ui-game → ui-home → ui-help → ui-stats → ui-settings → main.

Para volver a armar el archivo único después de editar:

```bash
python3 build.py . dist/calculo-mental.html
```
