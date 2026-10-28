# Cálculo mental · Notas y ritmo: descripción técnica para otra IA

Este documento explica la app **calculo-mental.html** para que otra IA (GPT, Gemini, etc.) pueda entenderla y modificarla sin tener que leer todo el código.

## Qué es

Es un entrenador de **multiplicación mental auditiva y espacial**. Los números de la cuenta no se ven: cada cifra se **escucha**. Hay dos **modos de sonido**:

- **♪ Notas**: cada cifra es una nota, que se compara con una nota de referencia.
- **🥁 Ritmo**: cada cifra es un ritmo que dura un pulso de negra.

Se sabe *dónde* está cada cifra porque hay casilleros blancos vacíos, y la respuesta se escribe con un teclado de botones blancos sin rótulo. Los números reales recién aparecen al corregir.

- Es un solo archivo HTML (HTML + CSS + JS), sin dependencias y sin red.
- Tiene interfaz en **español (por defecto) e inglés**. El idioma se elige arriba a la izquierda en la pantalla de inicio o en Configuración.
- Está pensado primero para celular (Chrome en Android) y se maneja con los pulgares. La pantalla de juego ocupa exactamente el alto de la pantalla (`100dvh`).
- Guarda todo en `localStorage` con el prefijo `cmz.`.
- El sonido se sintetiza en el momento con Web Audio API. No usa archivos de audio.

## Pantalla de inicio

```
[ES|EN]                         [? Ayuda]     ← menú de arriba
Cálculo mental
[   ♪ Notas    |    🥁 Ritmo   ]               ← modo de sonido (S.sound)
nivel adaptativo del modo elegido · ▶ Jugar
nivel fijo · última sesión · 📊 Estadísticas · ⚙ Configuración
```

**? Ayuda** abre `#scr-help` ("Cómo se juega"). Esa pantalla muestra las tablas de notas y de ritmos **tal como están configuradas en ese momento**.

## Código de notas (modo ♪)

Las notas son configurables. Se guardan como números MIDI en `S.refMidi` y `S.digitMidi[0..9]`, y la frecuencia se calcula con `midiF(m) = 440·2^((m−69)/12)`. Por defecto la referencia es **La3 = 220 Hz (MIDI 57)** y cada dígito *d* suena *d*+2 semitonos arriba (0 = Si3 … 9 = Sol#4).

El timbre es un piano eléctrico suave hecho con 4 parciales: seno ×1, seno ×2 con decaimiento rápido, triángulo ×1 y seno ×3.01, con una ADSR corta y un filtro pasabajos (`playFreq`). Las notas se nombran según el idioma: latinas en español (Do, Re…) y con letras en inglés (C, D…), con octava científica (`noteName`, `intervalName` en i18n.js).

## Código de ritmos (modo 🥁)

Cada cifra tiene un **patrón de texto** en `S.rhythms[0..9]`. Por defecto:

| Dígito | Patrón | Figura |
|---|---|---|
| 0 | `-` | silencio de negra (se marca con un golpe sordo) |
| 1 | `x` | negra |
| 2 | `xx` | 2 corcheas |
| 3 | `xxx` | tresillo de corcheas |
| 4 | `xxxx` | 4 semicorcheas |
| 5 | `xxxxx` | quintillo |
| 6 | `xxxxxx` | seisillo de semicorcheas |
| 7 | `xxxxxxx` | septillo |
| 8 | `xxxxxxxx` | 8 fusas |
| 9 | `xxx,xxxx` | tresillo de semicorcheas + 4 fusas |

**Sintaxis** (`parseRhythm` en rhythm.js):

- `x` = golpe, `X` = golpe con acento, `-` = silencio.
- Los caracteres de un grupo se reparten su tramo en partes iguales.
- La **coma** divide el pulso en tramos iguales.
- El **espacio** agrega otro pulso. Se permiten hasta 4 pulsos y hasta 16 signos por grupo.

`parseRhythm(src)` devuelve `{src, beats, ev:[{t, dur, note, acc, down}], restBeats:[i], struct}`, donde `t` y `dur` se miden en pulsos. Si el patrón es inválido, devuelve `null`. `rhythmOf(d)` devuelve el ritmo vigente de la cifra *d* y, si el guardado es inválido, usa el de fábrica.

**Sonidos del modo ritmo** (audio.js):

- `playHit`: golpe de bloque de madera. Es triángulo con caída de tono más un brillo corto.
- Si `S.rhythmPitch` está activo, cada golpe usa en cambio la nota de su cifra.
- Si `S.accentBeat` está activo, el primer golpe de cada pulso suena más fuerte. `X` acentúa siempre.
- `playRestMark`: golpe grave y sordo. Suena al inicio de **cada pulso entero en silencio**, como el 0.
- `playClick`: ruido agudo corto. Se usa para el conteo inicial (`S.countIn` clicks) y, si `S.beatClick` está activo, como metrónomo suave en cada pulso.

**Dibujo**: `rhythmSVG(R)` hace un SVG con notación simplificada. Tiene cabezas, plicas, barras (1 = corchea, 2 = semicorchea, 3 = fusa), el número de los grupos irregulares, el silencio de negra y silencios parciales. Usa `currentColor`. Aparece en la fila de referencia, en la ayuda y en el editor de Configuración.

## Capa de modo (audio.js)

El juego no sabe en qué modo está; usa estas funciones:

| Función | Notas | Ritmo |
|---|---|---|
| `unitMs()` | 60000 / `S.bpmMel` (una nota) | 60000 / `S.bpmRit` (un pulso de negra) |
| `digitItem(d, id)` | 1 unidad, una nota | `R.beats` unidades, el ritmo |
| `refItems()` | referencia + silencio (si `playBase`) | `countIn` clicks |
| `sepItems()` | 2 unidades de silencio | 2 pulsos sin marcar |
| `playDigitNow(d)` | nota suelta | vista previa del ritmo; corta la anterior |
| `playRefNow()` | nota de referencia | 4 clicks al tempo |

`playSeq(items, unitMs, onStep)` programa una secuencia en un bus propio que se puede cortar con `stopSeq()` y devuelve la duración en ms. Cada ítem tiene la forma `{u: unidades, fn: (t, bus, unitSec) => void | null, id?}`.

## Pantalla de juego (`#scr-game`): 4 niveles verticales

```
┌ barra: ✕ salir · nivel · n/N · cronómetro · ▶ tocar cuenta · Pasar ┐
│ NIVEL 1  [ref][0][1] … [9]   con nota (modo ♪) o dibujo de ritmo (🥁)  │
│ NIVEL 2  □□□□ / × □□ / ───   esquema de la cuenta (pasivo)            │
│ NIVEL 3  = [ ][ ][♪][♪]      registro de la respuesta (←)             │
│          línea de feedback (✔/✘, ▶ resultado, Seguir ⏎)              │
│ NIVEL 4  pad: la cuenta en botones vacíos          [▶]  ← sobre ⌫    │
│          teclado:  0 1 2 3 4 ⌫ / 5 6 7 8 9 ⏎  (blanco, sin rótulos)    │
└───────────────────────────────────────────────────────────────────────┘
```

1. **Nivel 1, `#lv1`** (`renderRefKeys()`, se redibuja en cada `startGame`). La primera tecla toca `playRefNow()`; las de los dígitos, `playDigitNow(d)`.
2. **Nivel 2, `#lv2`** (`vCells()`). Los ids son `vc-t-<i>` y `vc-b-<i>`. Tiene `pointer-events:none` y se ilumina con `.hl` mientras suena su cifra. En modo ritmo la iluminación dura lo que dura el ritmo.
3. **Nivel 3, `#lv3`** (`renderReg()`). `G.buf[0]` son las unidades. Al tocar un casillero suena su cifra.
4. **Nivel 4, `#lv4`**.
   - `.lv4top` es una grilla de 2 columnas: `#pad` y `#k-play` (▶).
   - La segunda columna mide `calc((100% - 30px)/6)`, igual que una columna de `#keys`, así el ▶ queda **exactamente arriba de ⌫**.
   - `renderPad()` calcula el ancho de los botones del pad descontando esa columna (mínimo 24 px), así 8×8 entra en 360 px.

Todos los botones de juego responden a `pointerdown`.

## Estado global `G`

```js
G = { disc:'mul', rk:'mul'|'mul_r', sm:'notes'|'rhythm', mode:'adapt'|'fixed', level, fixedLevel,
      prob, buf /* [0]=unidades */, phase:'answer'|'feedback'|'idle', t0, last, session, tick, advT }
```

## Flujo

```
startGame(mode, fixedLevel)   // fija G.sm y G.rk (rating del modo), dibuja nivel 1
  → nextProblem()  → playProblem() si autoPlay; el cronómetro arranca al terminar de sonar
  → press(k)       → autoJudge / ⏎ / Pasar → finish(ok, passed)
  → finish()       → nextRating sobre RATINGS[G.rk], HIST (con sm), sfx, revela números
  → advance()      → … hasta sessionLen → endSession()
```

## Dificultad adaptativa (`nextRating`)

Hay un **rating por modo**: `RATINGS.mul` (notas) y `RATINGS.mul_r` (ritmo). `ratingKey(disc)` arma la clave según `S.sound`. Hay 36 niveles, de 1×1 a 8×8, con objetivo `t = 8 + 6·a·b` segundos, iguales en los dos modos.

| Resultado | Cambio de rating |
|---|---|
| Acierto dentro de `t` | +0,25 |
| Acierto lento (≤ 2t) | +0,08 |
| Acierto muy lento | −0,1 |
| Error o pasar | −0,5 |

Al cambiar de nivel se entra en x,5. En el modo de nivel fijo el rating no cambia.

## Idiomas (i18n.js)

- `I18N.es` e `I18N.en` son diccionarios clave → texto con `{var}`, o función `(v) => html`, como en el caso de `help.body`.
- `t(clave, vars)` busca primero en el idioma activo, después en español, y si no encuentra devuelve la clave.
- Los elementos fijos del HTML llevan `data-i18n` / `data-i18n-aria` y se actualizan con `applyI18n()`.
- Las pantallas dinámicas se vuelven a dibujar con `t()`.
- `SETTINGS_UI` guarda claves de i18n, no textos.

## Configuración (`renderSettings`)

La tabla declarativa `SETTINGS_UI` (en storage.js) admite los tipos `bool`, `range`, `select` (con `str:true` si el valor es texto) y dos bloques especiales:

- `notesMap`: un select de nota (Do2…Do6) para la referencia y para cada cifra, con el intervalo al lado, ▶ para escuchar y un aviso si dos cifras suenan igual.
- `rhyMap`: un campo de texto con el patrón de cada cifra. Se guarda solo si es válido; si no, el borde se pone rojo y al salir del campo vuelve al último valor válido. Al lado tiene el dibujo y ▶.

Las secciones son: General (idioma, modo), Sonido y melodía (volumen, **bpm de la melodía**, duración de nota…), Ritmo (**bpm del ritmo**, conteo, acento, click, golpes con nota), Notas por cifra, Ritmos por cifra, Ayudas visuales y Juego.

"Restablecer" vuelve todo a los valores de fábrica, menos el idioma y el modo.

## localStorage (prefijo `cmz.`)

| Clave | Contenido |
|---|---|
| `cmz.settings` | objeto S. Se normaliza con `normSettings()`, que además migra `seqGapMs` de la v1 a `bpmMel`. |
| `cmz.ratings` | `{mul, mul_r}` |
| `cmz.hist` | array, máx. 5000: `{ts, d, sm:'n'|'r', m, l, q, a, u, ok, p, s, r}`. Si falta `sm`, se toma `'n'`. |
| `cmz.lastSession` / `cmz.lastSession_r` | resumen de la última sesión de cada modo |
| `cmz.fixedLevel` | último nivel fijo elegido |

Exportar e importar usan JSON `{app, v:2, settings, ratings, hist}`. También se pueden importar exportaciones v1. Las estadísticas se muestran por modo, con un selector arriba.

## Módulos (zip)

```
index.html          estructura de pantallas (inicio, juego, ayuda, estadísticas, configuración)
css/styles.css      estilos, mobile-first
js/storage.js       store, DEFAULTS, ritmos/notas por defecto, SETTINGS_UI, normSettings, S, RATINGS, HIST
js/i18n.js          I18N es/en, t(), applyI18n(), noteName, intervalName
js/rhythm.js        parseRhythm, rhythmOf, rhythmSVG
js/audio.js         síntesis (playFreq, playHit, playRestMark, playClick), playSeq, capa de modo
js/engine.js        P, genMul, DISCIPLINES, ratingKey, nextRating, levelOf
js/ui-common.js     $, show, flash, hl, formatos, segHTML/onSeg
js/ui-game.js       G, flujo de juego y render de los 4 niveles
js/ui-home.js       renderHome (menú de arriba, modo)
js/ui-help.js       renderHelp + tablas
js/ui-stats.js      renderStats por modo, export/import
js/ui-settings.js   renderSettings, editores de notas y ritmos
js/main.js          arranque
build.py            arma el HTML único
```

Son scripts clásicos, sin `type="module"`, para que funcionen abriendo `index.html` directo desde el disco (`file://`). Comparten el ámbito global y el **orden de carga importa**: storage → i18n → rhythm → audio → engine → ui-common → ui-game → ui-home → ui-help → ui-stats → ui-settings → main.

El HTML único se arma con `python3 build.py <carpeta> <salida.html>`, que pega el CSS y los JS dentro de `index.html` en ese orden.

## Teclado físico

Dígitos, Backspace, Enter (corrige o pasa a la siguiente), Espacio (▶ tocar la cuenta) y Esc (salir).
