# Design: Pagina Titolo "EYES" — HTML Autocontenuto

## Obiettivo

Creare una singola pagina HTML, completamente autocontenuta, che mostri il titolo "EYES" (dal file `asset/titolo.svg`) in alto a sinistra con margini generosi, su sfondo nero pieno. Nessun server, nessuna dipendenza esterna.

## Riferimenti

- SVG sorgente: `asset/titolo.svg` — viewBox `0 0 1920 1080`, testo bianco (`#fff`), stile `.st1` con `fill: #fff`.
- Pagina precedente nel progetto: `index.html` — webcam horror B&W, stessa estetica dark.

## Approccio scelto

**SVG inline** (opzione A): il contenuto di `asset/titolo.svg` viene incorporato direttamente come `<svg>` inline nel file HTML. Questo elimina dipendenze esterne e garantisce che la pagina funzioni aperta da qualsiasi browser locale.

## Struttura del file

```
title.html
├── <head>
│   ├── meta charset + viewport
│   └── <style> inline (fullscreen black, margini, sizing SVG)
└── <body>
    └── <svg> inline (path "EYES" da asset/titolo.svg)
```

## Stile e layout

| Elemento | Valore | Motivazione |
|----------|--------|-------------|
| Sfondo body | `#000` | Coerenza con estetica dark del progetto |
| Overflow | `hidden` | Nessuno scroll, pagina fissa |
| Padding body | `40px` | Margine uniforme su tutti i lati |
| SVG `max-width` | `400px` | Il viewBox 1920×1080 è molto largo; senza limite occuperebbe tutta la larghezza |
| SVG `height` | `auto` | Mantiene proporzioni originali |
| SVG `display` | `block` | Rimuove spazio fantasma inline sotto l'immagine |
| Colori | bianco su nero | Estratto dallo stile `.st1` del file Illustrator |

## HTML completo (struttura)

```html
<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>eyes</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  html, body {
    width: 100%;
    height: 100%;
    background: #000;
    overflow: hidden;
  }
  body {
    padding: 40px;
  }
  svg {
    display: block;
    max-width: 400px;
    height: auto;
    fill: #fff;
  }
</style>
</head>
<body>
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1920 1080">
  <path d="..." />
  <!-- tutti i path di asset/titolo.svg -->
</svg>
</body>
</html>
```

## Note per implementazione

- I `path` vengono copiati direttamente da `asset/titolo.svg`, rimuovendo i tag Illustrator (`<defs>` con `.st0`/`.st1`) e applicando `fill="#fff"` direttamente agli elementi, o mantenendo un `<style>` minimale inline.
- Il `viewBox="0 0 1920 1080"` è mantenuto per preservare le proporzioni del disegno originale.
- La pagina non contiene JavaScript: è puramente statica.

---

*Design approvato dall'utente. Procedere con writing-plans per il piano di implementazione.*
