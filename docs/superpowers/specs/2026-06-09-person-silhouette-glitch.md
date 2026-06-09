# Person Silhouette Glitch — Face/Body Tracking with ml5.js

## Obiettivo
Modificare l'effetto horror VHS in modo che la decomposizione (pixelazione, soglia binaria, drift, rumore) si applichi **solo alla sagoma della persona** (volto + corpo), mentre lo sfondo rimane il video pulito e a colori.

## Requisiti
- L'effetto segue il contorno preciso della persona (silhouette), non un bounding box
- Se più persone sono inquadrate, l'effetto si applica a tutte
- Lo sfondo rimane video originale a colori, non processato
- Se ml5.js non si carica o fallisce, fallback al glitch full-frame originale

## Stack
- ml5.js 1.x via CDN (`https://unpkg.com/ml5@1/dist/ml5.min.js`)
- MediaPipe SelfieSegmentation (modello bodySegmentation)
- Canvas 2D per compositing

## Architettura

### Pipeline modificata
```
loop()
  → getPersonMask()               // ml5.js ogni 3 frame
  → renderGlitchFrame()           // frame decomposto su glitchCanvas (offscreen)
  → compositePersonGlitch()       // maschera → incolla solo pixel persona
  → applyPostFX()                 // CRT bulge + chromatic shift (invariato)
```

### Nuovi elementi
- **`glitchCanvas` / `glitchCtx`**: offscreen canvas che contiene il frame completamente decomposto
- **`maskCanvas` / `maskCtx`**: offscreen canvas con la maschera binaria (255 = persona, 0 = sfondo)
- **`segmenter`**: istanza ml5.js `bodySegmentation('SelfieSegmentation')`
- **`frameSkip`**: contatore per eseguire segmentation ogni 3 frame

### Composite in dettaglio
1. Video pulito → main canvas (sfondo)
2. `renderGlitchFrame()` produce frame decomposto su `glitchCanvas`
3. `maskCanvas` ha maschera binaria ridimensionata alle dimensioni del canvas
4. `maskCtx.globalCompositeOperation = 'source-in'` + `drawImage(glitchCanvas, 0, 0)` → maskCtx ora contiene glitch solo dove c'è la persona
5. `ctx.drawImage(maskCanvas, 0, 0)` → main canvas: persona glitchata su sfondo pulito
6. `applyPostFX()` invariata

### renderGlitchFrame()
Identica a `renderFrame()` originale, ma scrive su `glitchCanvas` invece che su main canvas.

### applyPostFX()
Invariata — si applica al main canvas dopo il composite.

### Fallback
Se `segmenter` non è pronto (non caricato, errore, o primo frame), si usa la `renderFrame()` originale su main canvas (glitch full-frame).

## Caricamento modello
- Script CDN ml5.js nel `<head>`
- Dopo `startWebcam()`, caricare modello `SelfieSegmentation`
- Stato UI: "CARICAMENTO MODELLO..." → "SEGNALE INSTABILE" quando pronto
- Primavera: ~2-4MB download

## Modifiche al codice esistente
- `renderFrame()` diventa `renderGlitchFrame()` e scrive su `glitchCanvas`
- Nuova funzione `getPersonMask()` chiamata ogni 3 frame
- Nuova funzione `compositePersonGlitch()` per il compositing
- `loop()` modificata con branch fallback
- Nessun nuovo slider UI — impostazioni esistenti (CONTRASTO, PIXEL, NOISE) valide
- Nessuna modifica a `applyPostFX()`

## Dipendenze
- ml5.js 1.x via CDN
- SelfieSegmentation model MediaPipe
- Nessuna altra modifica alle dipendenze esistenti
