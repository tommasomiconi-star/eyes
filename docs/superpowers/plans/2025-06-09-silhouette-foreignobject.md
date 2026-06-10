# Integrazione Webcam in SVG via `foreignObject` — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Incorporare il video webcam con effetto B&W decomposto dentro `looking copia.svg`, posizionato nella zona bassa (x=492, y=1700, w=1000, h=900), usando `\u003cforeignObject\u003e`.

**Architecture:** L'SVG esistente (`looking copia.svg`) viene arricchito con un `\u003cforeignObject\u003e` come ultimo elemento visivo. Al suo interno un blocco XHTML (`\u003cdiv\u003e`) contiene il canvas, il video nascosto, i controlli e tutto il CSS/JS copiato da `index.html`. Il JavaScript rimane identico eccetto per l'adattamento del sizing del canvas alla viewport del `foreignObject`. Nessun file nuovo viene creato — solo `looking copia.svg` viene modificato; `index.html` rimane inalterato.

**Tech Stack:** SVG 1.1/2.0, HTML5 `foreignObject`, Canvas 2D, ml5.js 1.x (CDN), vanilla JS.

---

### Task 1: Preparare la struttura SVG con `\u003cforeignObject\u003e`

**Files:**
- Modify: `looking copia.svg`

- [ ] **Step 1: Verificare namespace XHTML**

Controlla che il tag root `\u003csvg\u003e` contenga il namespace XHTML:
```xml
xmlns="http://www.w3.org/2000/svg"
```
(è già presente nel file esistente). Non serve aggiungerne un altro.

- [ ] **Step 2: Inserire `\u003cforeignObject\u003e` prima di `\u003c/svg\u003e`**

Nel file `looking copia.svg`, dopo l'ultimo elemento `\u003cuse\u003e` (riga 26) e prima della chiusura `\u003c/svg\u003e` (riga 27), aggiungi:

```xml
  \u003cforeignObject x="492" y="1700" width="1000" height="900"\u003e
    \u003cdiv xmlns="http://www.w3.org/1999/xhtml" style="width:100%;height:100%;background:transparent;position:relative;"\u003e
      \u003ccanvas id="mainCanvas" style="width:100%;height:100%;display:block;image-rendering:pixelated;"\u003e\u003c/canvas\u003e
      \u003cvideo id="video" autoplay playsinline style="display:none;"\u003e\u003c/video\u003e
      \u003cdiv id="overlay" style="position:absolute;bottom:12px;left:50%;transform:translateX(-50%);text-align:center;z-index:10;"\u003e
        \u003cbutton id="startBtn" style="background:#111;color:#888;border:1px solid #333;padding:8px 20px;font-family:'Courier New',monospace;font-size:11px;cursor:pointer;letter-spacing:2px;text-transform:uppercase;"\u003e▶ attiva webcam\u003c/button\u003e
        \u003cdiv id="status" style="color:#444;font-size:9px;letter-spacing:3px;margin-top:6px;text-transform:uppercase;"\u003eSURVEILLANCE FEED // SIGNAL UNSTABLE\u003c/div\u003e
      \u003c/div\u003e
      \u003cbutton id="settingsBtn" style="position:absolute;top:8px;right:8px;z-index:20;background:none;border:1px solid #333;color:#555;font-family:'Courier New',monospace;font-size:10px;padding:4px 8px;cursor:pointer;letter-spacing:2px;text-transform:uppercase;"\u003e⚙\u003c/button\u003e
      \u003cdiv id="settingsPanel" style="position:absolute;top:30px;right:8px;z-index:20;background:#080808;border:1px solid #222;padding:12px 14px;display:none;min-width:140px;"\u003e
        \u003cdiv style="margin-bottom:8px;"\u003e
          \u003clabel style="display:block;color:#555;font-size:8px;letter-spacing:2px;text-transform:uppercase;margin-bottom:2px;"\u003eCONTRASTO \u003cspan id="contrastVal" style="color:#444;font-size:8px;float:right;"\u003e50\u003c/span\u003e\u003c/label\u003e
          \u003cinput type="range" id="contrastSlider" min="0" max="100" value="50" style="-webkit-appearance:none;appearance:none;width:100%;height:2px;background:#222;outline:none;border:none;"\u003e
        \u003c/div\u003e
        \u003cdiv style="margin-bottom:8px;"\u003e
          \u003clabel style="display:block;color:#555;font-size:8px;letter-spacing:2px;text-transform:uppercase;margin-bottom:2px;"\u003ePIXEL \u003cspan id="pixelVal" style="color:#444;font-size:8px;float:right;"\u003e50\u003c/span\u003e\u003c/label\u003e
          \u003cinput type="range" id="pixelSlider" min="0" max="100" value="50" style="-webkit-appearance:none;appearance:none;width:100%;height:2px;background:#222;outline:none;border:none;"\u003e
        \u003c/div\u003e
        \u003cdiv style="margin-bottom:0;"\u003e
          \u003clabel style="display:block;color:#555;font-size:8px;letter-spacing:2px;text-transform:uppercase;margin-bottom:2px;"\u003eNOISE \u003cspan id="noiseVal" style="color:#444;font-size:8px;float:right;"\u003e50\u003c/span\u003e\u003c/label\u003e
          \u003cinput type="range" id="noiseSlider" min="0" max="100" value="50" style="-webkit-appearance:none;appearance:none;width:100%;height:2px;background:#222;outline:none;border:none;"\u003e
        \u003c/div\u003e
      \u003c/div\u003e
    \u003c/div\u003e
  \u003c/foreignObject\u003e
```

**Nota:** tutti gli stili sono inline per evitare conflitti con `.st0` dell'SVG.

---

### Task 2: Inserire il CSS slider dentro gli stili inline

**Files:**
- Modify: `looking copia.svg` (continuazione)

- [ ] **Step 1: Aggiungere stili thumb slider inline**

Per completare l'aspetto dei range input, aggiungi subito dopo gli stili del `\u003cinput\u003e` una piccola regola CSS nel blocco `\u003cdiv\u003e` del foreignObject. Poiché `\u003cstyle\u003e` non è valido dentro `foreignObject` senza `\u003cdefs\u003e`, utilizza un `\u003cstyle\u003e` dedicato all'interno dello stesso `\u003cdiv\u003e` radice:

```xml
      \u003cstyle\u003e
        input[type="range"]::-webkit-slider-thumb {
          -webkit-appearance: none;
          appearance: none;
          width: 8px;
          height: 8px;
          background: #555;
          cursor: pointer;
          border: none;
        }
        input[type="range"]::-moz-range-thumb {
          width: 8px;
          height: 8px;
          background: #555;
          cursor: pointer;
          border: none;
        }
        #settingsPanel.visible { display: block !important; }
      \u003c/style\u003e
```

Inseriscilo all'interno del `\u003cdiv xmlns="http://www.w3.org/1999/xhtml"\u003e`, prima del `\u003ccanvas\u003e`.

---

### Task 3: Inserire lo script applicativo (copia da `index.html`)

**Files:**
- Modify: `looking copia.svg` (continuazione)
- Read: `index.html` (riferimento — già letto in precedenza)

- [ ] **Step 1: Aggiungere il tag script ml5.js**

Prima della chiusura `\u003c/svg\u003e`, aggiungi:
```xml
  \u003cscript xlink:href="https://unpkg.com/ml5@1/dist/ml5.js"\u003e\u003c/script\u003e
```

- [ ] **Step 2: Aggiungere lo script applicativo con adattamento sizing**

Subito dopo, aggiungi un `\u003cscript\u003e\u003c![CDATA[` contenente tutto il JavaScript di `index.html`, con queste modifiche:

**Modifica A — `sizeCanvas()`:**
Invece di usare `window.innerWidth/Height`, imposta il canvas alla risoluzione nativa del `foreignObject` in pixel dello schermo. Calcola via `getBoundingClientRect`:

```javascript
function sizeCanvas() {
  const fo = document.querySelector('foreignObject');
  const rect = fo ? fo.getBoundingClientRect() : { width: 1000, height: 900 };
  canvas.width = Math.max(1, Math.floor(rect.width));
  canvas.height = Math.max(1, Math.floor(rect.height));
}
```

**Modifica B — resize handler:**
```javascript
window.addEventListener('resize', sizeCanvas);
```
rimane invariato.

**Modifica C — pannello settings toggle:**
Verifica che il CSS class `.visible` funzioni correttamente. Lo script esistente usa:
```javascript
settingsBtn.addEventListener('click', () => settingsPanel.classList.toggle('visible'));
```
Questo funziona perché nel Task 2 abbiamo aggiunto la regola `#settingsPanel.visible { display: block !important; }`.

Il resto dello script (`macroblockRow`, `renderFrame`, `renderGlitchFrame`, `gotSegmentation`, `compositePersonGlitch`, `applyPostFX`, `loop`, `startWebcam`, slider setup) rimane **identico** a `index.html`.

---

### Task 4: Verifica struttura SVG finale

**Files:**
- Modify: `looking copia.svg` (eventuali fix di sintassi)

- [ ] **Step 1: Validare XML**

Controlla che l'SVG finale abbia questa struttura:

```xml
\u003c?xml version="1.0" encoding="UTF-8"?\u003e
\u003csvg ...\u003e
  \u003cdefs\u003e
    \u003cstyle\u003e...\u003c/style\u003e
    \u003cimage id="image" .../\u003e
    \u003cimage id="image1" .../\u003e
  \u003c/defs\u003e
  \u003ctext class="st0" ...\u003e...\u003c/text\u003e
  \u003cuse .../\u003e
  ...
  \u003cforeignObject x="492" y="1700" width="1000" height="900"\u003e
    \u003cdiv xmlns="http://www.w3.org/1999/xhtml"\u003e
      \u003cstyle\u003e...\u003c/style\u003e
      \u003ccanvas id="mainCanvas" ...\u003e\u003c/canvas\u003e
      \u003cvideo id="video" ...\u003e\u003c/video\u003e
      \u003cdiv id="overlay"\u003e...\u003c/div\u003e
      \u003cbutton id="settingsBtn"\u003e...\u003c/button\u003e
      \u003cdiv id="settingsPanel"\u003e...\u003c/div\u003e
    \u003c/div\u003e
  \u003c/foreignObject\u003e
  \u003cscript xlink:href="https://unpkg.com/ml5@1/dist/ml5.js"\u003e\u003c/script\u003e
  \u003cscript\u003e\u003c![CDATA[
    ...app JS...
  ]]\u003e\u003c/script\u003e
\u003c/svg\u003e
```

Assicurarsi che:
- Il namespace `xmlns="http://www.w3.org/1999/xhtml"` sia presente sul `\u003cdiv\u003e` radice del `foreignObject`
- I CDATA dello script siano chiusi correttamente (`]]\u003e`)
- Il tag `\u003cscript\u003e` con `xlink:href` per ml5.js sia **prima** dello script applicativo
- Nessun elemento manchi o sia mal chiuso

---

### Task 5: Test manuale in browser

**Files:**
- Test: `looking copia.svg` (apertura browser)

- [ ] **Step 1: Aprire in browser**

```bash
open "looking copia.svg"
```

**Expected:**
- Browser apre il file
- Si vede il testo "WE ARE LOOKING" in alto
- Si vedono le immagini degli occhi sparse
- In basso, al centro, appare il pulsante "▶ attiva webcam"
- Nessun errore nella console del browser (F12 → Console)

- [ ] **Step 2: Attivare webcam**

Clicca "attiva webcam".

**Expected:**
- Browser richiede permesso webcam
- Dopo il consenso, nella zona bassa appare il video B&W decomposto della silhouette
- I controlli "⚙" funzionano e mostrano i tre slider

- [ ] **Step 3: Verificare fallback SVG statico**

Aprire `looking copia.svg` in un viewer statico (es. QuickLook macOS, Inkscape, Illustrator).

**Expected:**
- Il `foreignObject` viene ignorato
- Si vedono solo testo e immagini occhi originali
- Nessun errore, nessun elemento strano

---

### Task 6: Commit

**Files:**
- Commit: `looking copia.svg`

- [ ] **Step 1: Stage e commit**

```bash
git add looking\ copia.svg
git commit -m "feat: integrate webcam silhouette glitch into SVG via foreignObject"
```

---

## Spec coverage check

| Requisito spec | Task che lo implementa |
|----------------|------------------------|
| `\u003cforeignObject\u003e` in zona bassa (x=492, y=1700, w=1000, h=900) | Task 1, Step 2 |
| `\u003cdiv\u003e` con namespace XHTML | Task 1, Step 2 |
| Canvas + video + controlli UI | Task 1, Step 2 |
| CSS inline per evitare conflitti `.st0` | Task 1, Step 2; Task 2, Step 1 |
| JavaScript identico con adattamento sizing | Task 3, Step 2 |
| Script ml5.js esterno | Task 3, Step 1 |
| Fallback viewer statici | Implicito (foreignObject ignorato) |
| Verifica struttura XML | Task 4 |
| Test manuale browser | Task 5 |

Nessun gap rilevato.

## Placeholder scan

- Nessun "TBD", "TODO", "implement later" presente.
- Ogni step mostra il codice XML/HTML/JS esatto da inserire.
- I comandi `open` e `git` sono espliciti.
- Nessun riferimento a funzioni non definite.

## Type / naming consistency

- Gli ID del DOM (`mainCanvas`, `video`, `startBtn`, `status`, `settingsBtn`, `settingsPanel`, `contrastSlider`, `pixelSlider`, `noiseSlider`, `contrastVal`, `pixelVal`, `noiseVal`) corrispondono esattamente a quelli usati in `index.html`.
- I nomi funzione (`sizeCanvas`, `startWebcam`, `renderFrame`, `renderGlitchFrame`, `gotSegmentation`, `compositePersonGlitch`, `applyPostFX`, `loop`) sono identici a `index.html`.
- La classe CSS `.visible` per toggle del pannello è coerente tra Task 2 (definizione) e Task 3 (uso in JS).

---

**Piano completato e salvato in `docs/superpowers/plans/2025-06-09-silhouette-foreignobject.md`.**
