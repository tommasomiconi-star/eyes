# Pagina Titolo "EYES" — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Creare una singola pagina HTML (`title.html`) che mostri il titolo "EYES" in SVG inline, posizionato in alto a sinistra con margini, su sfondo nero pieno. Nessun server, nessuna dipendenza esterna.

**Architecture:** File HTML autocontenuto con SVG inline. Il CSS è inline nel `<head>`, i path SVG sono copiati direttamente da `asset/titolo.svg`. Nessun JavaScript.

**Tech Stack:** HTML5, CSS3 inline, SVG inline.

---

## File Structure

| File | Azione | Descrizione |
|------|--------|-------------|
| `title.html` | **Crea** | Pagina completa con struttura HTML, CSS inline, SVG inline con path "EYES" |
| `asset/titolo.svg` | **Leggi** | Fonte dei path SVG da copiare |

---

### Task 1: Scaffolding HTML e CSS

**Files:**
- Create: `title.html`

- [ ] **Step 1: Scrivi la struttura HTML con CSS inline**

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
</style>
</head>
<body>
  <!-- SVG verrà inserito nel Task 2 -->
</body>
</html>
```

- [ ] **Step 2: Verifica struttura base**

Run: `test -f title.html && echo "File esiste" || echo "File mancante"`
Expected: `File esiste`

Run: `grep -q "background: #000" title.html && echo "Sfondo nero presente" || echo "Sfondo nero mancante"`
Expected: `Sfondo nero presente`

Run: `grep -q "padding: 40px" title.html && echo "Margini presenti" || echo "Margini mancanti"`
Expected: `Margini presenti`

- [ ] **Step 3: Commit parziale**

```bash
git add title.html
git commit -m "feat: scaffolding title.html con sfondo nero e margini"
```

---

### Task 2: Inserimento SVG inline

**Files:**
- Modify: `title.html`
- Read: `asset/titolo.svg`

- [ ] **Step 1: Leggi i path da `asset/titolo.svg`**

Il file `asset/titolo.svg` contiene:
- `viewBox="0 0 1920 1080"`
- Gruppo con classi Illustrator `.st0` e `.st1`
- Path della parola "EYES" (lettere maiuscole) in bianco (`fill="#fff"`)

- [ ] **Step 2: Inserisci SVG inline nel body**

Modifica `title.html` sostituendo il commento placeholder con:

```html
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1920 1080" style="display:block;max-width:400px;height:auto;fill:#fff;">
  <!-- Tutti i path di EYES da asset/titolo.svg -->
  <path d="M667.09,549.43l-22.54-90.51h-.37l-22.16,90.51h-29.61l-35.2-132.98h29.24l21.05,90.51h.37l23.09-90.51h27.38l22.72,91.63h.37l21.79-91.63h28.68l-35.76,132.98h-29.05Z"/>
  <path d="M844.76,416.46v24.58h-70.21v28.5h64.44v22.72h-64.44v32.59h71.7v24.58h-100.94v-132.98h99.45Z"/>
  <path d="M984.07,416.46l49.73,132.98h-30.36l-10.06-29.61h-49.73l-10.43,29.61h-29.43l50.29-132.98h29.98ZM985.74,498.03l-16.76-48.79h-.37l-17.32,48.79h34.45Z"/>
  <path d="M1117.04,416.46c5.96,0,11.33.96,16.11,2.89,4.78,1.93,8.88,4.56,12.29,7.92,3.41,3.35,6.02,7.23,7.82,11.64,1.8,4.41,2.7,9.16,2.7,14.25,0,7.82-1.65,14.59-4.94,20.3-3.29,5.71-8.66,10.06-16.11,13.04v.37c3.6,1,6.58,2.51,8.94,4.56,2.36,2.05,4.28,4.47,5.77,7.26,1.49,2.79,2.58,5.87,3.26,9.22.68,3.35,1.15,6.7,1.4,10.06.12,2.11.25,4.59.37,7.45.12,2.86.34,5.77.65,8.75.31,2.98.81,5.81,1.49,8.47.68,2.67,1.71,4.94,3.07,6.8h-29.24c-1.62-4.22-2.61-9.25-2.98-15.08-.37-5.83-.93-11.42-1.68-16.76-1-6.95-3.11-12.04-6.33-15.27-3.23-3.23-8.51-4.84-15.83-4.84h-29.24v51.96h-29.24v-132.98h71.7ZM1106.61,476.61c6.71,0,11.73-1.49,15.09-4.47,3.35-2.98,5.03-7.82,5.03-14.53s-1.68-11.14-5.03-14.06c-3.35-2.92-8.38-4.38-15.09-4.38h-32.03v37.43h32.03Z"/>
  <path d="M1279.26,416.46v24.58h-70.21v28.5h64.44v22.72h-64.44v32.59h71.7v24.58h-100.94v-132.98h99.45Z"/>
  <path d="M598.74,576.79v108.39h64.81v24.58h-94.05v-132.98h29.24Z"/>
  <path d="M678.64,616.18c2.98-8.5,7.26-15.92,12.85-22.25,5.59-6.33,12.45-11.3,20.58-14.9,8.13-3.6,17.29-5.4,27.47-5.4s19.49,1.8,27.56,5.4c8.07,3.6,14.9,8.57,20.49,14.9,5.59,6.33,9.87,13.75,12.85,22.25,2.98,8.51,4.47,17.73,4.47,27.66s-1.49,18.72-4.47,27.1c-2.98,8.38-7.26,15.68-12.85,21.88-5.59,6.21-12.42,11.08-20.49,14.62-8.07,3.54-17.26,5.31-27.56,5.31s-19.34-1.77-27.47-5.31c-8.13-3.54-14.99-8.41-20.58-14.62-5.59-6.21-9.87-13.5-12.85-21.88s-4.47-17.41-4.47-27.1,1.49-19.15,4.47-27.66ZM705.36,660.13c1.3,5.28,3.38,10.03,6.24,14.25,2.85,4.22,6.58,7.61,11.17,10.15,4.59,2.55,10.18,3.82,16.76,3.82s12.17-1.27,16.76-3.82c4.59-2.54,8.32-5.93,11.17-10.15,2.85-4.22,4.94-8.97,6.24-14.25,1.3-5.28,1.96-10.71,1.96-16.3s-.65-11.48-1.96-16.95c-1.3-5.46-3.38-10.34-6.24-14.62-2.86-4.28-6.58-7.7-11.17-10.24-4.59-2.54-10.18-3.82-16.76-3.82s-12.17,1.27-16.76,3.82c-4.59,2.55-8.32,5.96-11.17,10.24-2.86,4.28-4.94,9.16-6.24,14.62-1.3,5.46-1.96,11.11-1.96,16.95s.65,11.02,1.96,16.3Z"/>
  <path d="M823.53,616.18c2.98-8.5,7.26-15.92,12.85-22.25,5.59-6.33,12.45-11.3,20.58-14.9,8.13-3.6,17.29-5.4,27.47-5.4s19.49,1.8,27.56,5.4c8.07,3.6,14.9,8.57,20.49,14.9,5.59,6.33,9.87,13.75,12.85,22.25,2.98,8.51,4.47,17.73,4.47,27.66s-1.49,18.72-4.47,27.1c-2.98,8.38-7.26,15.68-12.85,21.88-5.59,6.21-12.42,11.08-20.49,14.62-8.07,3.54-17.26,5.31-27.56,5.31s-19.34-1.77-27.47-5.31c-8.13-3.54-14.99-8.41-20.58-14.62-5.59-6.21-9.87-13.5-12.85-21.88s-4.47-17.41-4.47-27.1,1.49-19.15,4.47-27.66ZM850.26,660.13c1.3,5.28,3.38,10.03,6.24,14.25,2.85,4.22,6.58,7.61,11.17,10.15,4.59,2.55,10.18,3.82,16.76,3.82s12.17-1.27,16.76-3.82c4.59-2.54,8.32-5.93,11.17-10.15,2.85-4.22,4.94-8.97,6.24-14.25,1.3-5.28,1.96-10.71,1.96-16.3s-.65-11.48-1.96-16.95c-1.3-5.46-3.38-10.34-6.24-14.62-2.86-4.28-6.58-7.7-11.17-10.24-4.59-2.54-10.18-3.82-16.76-3.82s-12.17,1.27-16.76,3.82c-4.59,2.55-8.32,5.96-11.17,10.24-2.86,4.28-4.94,9.16-6.24,14.62-1.3,5.46-1.96,11.11-1.96,16.95s.65,11.02,1.96,16.3Z"/>
  <path d="M998.97,576.79v55.13l51.96-55.13h36.5l-51.96,52.52,56.99,80.46h-36.69l-40.04-59.78-16.76,16.95v42.83h-29.24v-132.98h29.24Z"/>
  <path d="M1133.43,576.79v132.98h-29.24v-132.98h29.24Z"/>
  <path d="M1188.19,576.79l55.5,89.21h.37v-89.21h27.38v132.98h-29.24l-55.31-89.02h-.37v89.02h-27.38v-132.98h29.05Z"/>
  <path d="M1375.91,708.74c-6.33,2.67-12.73,4-19.18,4-10.18,0-19.34-1.77-27.47-5.31-8.13-3.54-14.99-8.41-20.58-14.62-5.59-6.21-9.87-13.5-12.85-21.88-2.98-8.38-4.47-17.41-4.47-27.1s1.49-19.15,4.47-27.66c2.98-8.5,7.26-15.92,12.85-22.25,5.59-6.33,12.45-11.3,20.58-14.9,8.13-3.6,17.29-5.4,27.47-5.4,6.83,0,13.44,1.02,19.83,3.07,6.39,2.05,12.17,5.06,17.32,9.03,5.15,3.98,9.41,8.88,12.76,14.71,3.35,5.84,5.4,12.54,6.15,20.11h-27.94c-1.74-7.45-5.09-13.04-10.06-16.76-4.97-3.72-10.99-5.59-18.06-5.59-6.58,0-12.17,1.27-16.76,3.82-4.6,2.55-8.32,5.96-11.17,10.24-2.86,4.28-4.93,9.16-6.24,14.62-1.3,5.46-1.96,11.11-1.96,16.95s.65,11.02,1.96,16.3c1.3,5.28,3.38,10.03,6.24,14.25,2.85,4.22,6.58,7.61,11.17,10.15,4.59,2.55,10.18,3.82,16.76,3.82,9.68,0,17.16-2.45,22.44-7.36,5.28-4.9,8.35-12.01,9.22-21.32h-29.43v-21.79h55.87v71.89h-18.62l-2.98-15.08c-5.21,6.7-10.99,11.39-17.32,14.06Z"/>
</svg>
```

- [ ] **Step 3: Verifica presenza SVG e path**

Run: `grep -q '<svg xmlns="http://www.w3.org/2000/svg"' title.html && echo "SVG presente" || echo "SVG mancante"`
Expected: `SVG presente`

Run: `grep -c '<path d=' title.html`
Expected: `11` (10 path EYES + 1 path di chiusura)

Run: `grep -q 'max-width:400px' title.html && echo "Sizing SVG presente" || echo "Sizing SVG mancante"`
Expected: `Sizing SVG presente`

- [ ] **Step 4: Verifica visiva nel browser**

Apri `title.html` in un browser locale. Verifica che:
1. Lo sfondo è completamente nero
2. La scritta "EYES" è visibile in bianco in alto a sinistra
3. C'è un margine di ~40px dai bordi
4. La larghezza del titolo non supera ~400px
5. Non ci sono barre di scorrimento

- [ ] **Step 5: Commit finale**

```bash
git add title.html
git commit -m "feat: pagina titolo EYES in SVG inline su sfondo nero"
```

---

## Self-Review

**1. Spec coverage:**
- ✅ Singola pagina HTML autocontenuta — coperto in Task 1
- ✅ Titolo in alto a sinistra — coperto con `padding: 40px` e SVG come primo elemento del body
- ✅ Margini — coperto con `padding: 40px`
- ✅ Sfondo nero pieno — coperto con `background: #000`
- ✅ Nessun server — file singolo, nessuna dipendenza
- ✅ SVG inline — coperto in Task 2
- ✅ Funziona senza server di sviluppo — implicito, file statico

**2. Placeholder scan:**
- ✅ Nessun "TBD", "TODO", "implement later"
- ✅ Nessun "add appropriate error handling"
- ✅ Codice completo in ogni step
- ✅ Comandi di test con expected output espliciti

**3. Type consistency:**
- ✅ `title.html` creato in Task 1 e modificato in Task 2 — stesso file, percorso coerente
- ✅ `asset/titolo.svg` referenziato come sorgente lettura, mai modificato

---

## Execution Handoff

**Plan complete and saved to `docs/superpowers/plans/2026-06-10-title-page.md`.**

Two execution options:

**1. Subagent-Driven (recommended)** — I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** — Execute tasks in this session using executing-plans, batch execution with checkpoints

Which approach?
