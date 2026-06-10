# Design: Integrazione Webcam Silhouette in SVG via `<foreignObject>`

## Obiettivo

Inserire il video della webcam con effetto horror B&W decomposto (già implementato in `index.html`) all'interno del file `looking copia.svg`, posizionato nella **zona bassa** del disegno, senza occupare la parte superiore dove sono presenti il testo "WE ARE LOOKING" e le immagini degli occhi.

## Riferimenti

- SVG target: `looking copia.svg` — viewBox `0 0 1984.25 2834.65`, testo in alto fino a y≈700, immagini sparse fino a y≈2600.
- Sorgente effetto webcam: `index.html` — webapp single-page con Canvas 2D, ml5.js `bodySegmentation`, effetto B&W binario con decomposizione su tiles.

## Decisione architetturale

Utilizzare `<foreignObject>` inserito come ultimo elemento dell'SVG. Questo permette di incorporare un blocco HTML5 (`<div>` + `<canvas>` + `<video>` + controlli) direttamente dentro il file `.svg`, mantenendo il disegno vettoriale intatto e aggiungendo l'interattività solo quando il file viene aperto in un browser moderno.

## Posizionamento nel viewBox SVG

| Proprietà | Valore | Motivazione |
|-----------|--------|-------------|
| `x` | `492` | Centrato orizzontalmente (1984.25 − 1000) / 2 |
| `y` | `1700` | Sotto il testo principale, sopra le immagini più basse |
| `width` | `1000` | Ampia ma non a bordi, lascia margine laterale |
| `height` | `900` | Area verticale sufficiente per una silhouette umana |

Il `<foreignObject>` viene piazzato **dopo** tutti gli altri elementi SVG (testo, `<use>` immagini), così z-index nativo SVG lo rende visibile sopra gli elementi precedenti ma sotto eventuali overlay futuri.

## Struttura del `<foreignObject>`

```xml
<foreignObject x="492" y="1700" width="1000" height="900">
  <div xmlns="http://www.w3.org/1999/xhtml"
       style="width:100%;height:100%;background:transparent;position:relative;">
    <canvas id="mainCanvas"
            style="width:100%;height:100%;display:block;image-rendering:pixelated;"></canvas>
    <video id="video" autoplay playsinline style="display:none;"></video>
    <!-- UI overlay: pulsante attiva, status, pannello settings -->
    <div id="overlay" style="position:absolute;bottom:12px;left:50%;transform:translateX(-50%);">
      <button id="startBtn">▶ attiva webcam</button>
      <div id="status">SURVEILLANCE FEED // SIGNAL UNSTABLE</div>
    </div>
    <button id="settingsBtn" style="position:absolute;top:8px;right:8px;">⚙</button>
    <div id="settingsPanel" style="position:absolute;top:36px;right:8px;">…sliders…</div>
  </div>
</foreignObject>
```

### CSS in `<foreignObject>`

Il CSS dei controlli viene iniettato inline nello stesso blocco HTML oppure come `<style>` dedicato nel `<defs>` dell'SVG. Per evitare conflitti con `.st0` esistente, tutti gli ID e le classi del sottosistema webcam usano prefissi univoci (già presenti in `index.html`).

## JavaScript integrato

L'intero `<script>` di `index.html` (≈480 righe) viene copiato e incollato come `<script>` all'interno dell'SVG, **prima della chiusura `</svg>`**. Nessuna modifica alla logica:

1. `startWebcam()` → `getUserMedia` + caricamento ml5 segmenter
2. `loop()` via `requestAnimationFrame`
3. Se maschera disponibile: `renderGlitchFrame()` → `compositePersonGlitch()`
4. Altrimenti fallback: `renderFrame()`
5. Sempre: `applyPostFX()`

### Adattamento dimensioni canvas

Nel `sizeCanvas()` originale il canvas occupa `window.innerWidth/Height`. Nel nuovo contesto SVG, il canvas deve occupare il 100% del `<foreignObject>`. Il resize handler viene modificato per leggere `foreignObject.getBoundingClientRect()` anziché `window`, oppure si usa il CSS `width:100%;height:100%` e si lascia che il browser gestisca la scalatura automatica del canvas (l'attributo `style` del `<canvas>` copre già questo caso).

## Pipeline di rendering (nessuna modifica logica)

| Fase | Canvas | Operazione |
|------|--------|------------|
| Capture | `offCanvas` | `drawImage(video)` a risoluzione nativa |
| Glitch | `glitchCanvas` | Decomposizione tiles B&W invertito |
| Maschera | `maskCanvas` | ml5 segmentation → binary mask (persona opaca, sfondo trasparente) |
| Compositing | `mainCanvas` | Clean video background + `source-in` glitch mask + overlay |
| PostFX | `mainCanvas` | CRT bulge LUT + chromatic shift |

## Fallback e compatibilità

- **Browser moderni** (Chrome, Firefox, Safari, Edge): `<foreignObject>` supportato nativamente. L'utente vede il disegno completo con la webcam interattiva in basso.
- **Viewer SVG statici** (Inkscape, Illustrator, quicklook): `<foreignObject>` ignorato. L'utente vede solo l'SVG originale (testo + immagini occhi), nessun errore visibile.
- **ml5.js non caricato / CORS / modello non pronto**: `renderFrame()` fallback mostra glitch B&W sull'intero frame, nessun crash.

## File di output

Il risultato finale è un **singolo file** modificato:

- `looking copia.svg` — contiene testo, immagini occhi, `<foreignObject>` con HTML/CSS/JS della webcam.

`index.html` rimane inalterato come riferimento / versione standalone.

## Note per implementazione

- Verificare che lo `xmlns="http://www.w3.org/1999/xhtml"` sia presente sul `<div>` radice del `<foreignObject>`, altrimenti il parser SVG potrebbe rifiutare i tag HTML.
- Il `<script>` dell'SVG deve usare `document.getElementById()` con ID univoci. Gli ID già usati in `index.html` (`mainCanvas`, `video`, `startBtn`, ecc.) sono sufficientemente univoci all'interno del singolo file.
- Per evitare che il pannello settings esca dai bordi del `foreignObject` (1000×900), i controlli vengono scalati in proporzione: font più piccolo, padding ridotto, slider compatti.
- Il caricamento ml5.js (`<script src="https://unpkg.com/ml5@1/dist/ml5.js">`) deve essere mantenuto come tag esterno, preferibilmente **prima** dello script applicativo, oppure verificare `window.ml5` disponibile prima di usarlo.

---

*Design approvato dall'utente. Procedere con writing-plans per il piano di implementazione.*
