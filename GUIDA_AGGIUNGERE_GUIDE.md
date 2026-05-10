# Come aggiungere una nuova Guida dello Studio

## Cosa serve

1. **File HTML** della guida (es. `adempimenti-assunzione.html`) nella root del progetto
2. **Una riga** nell'array JS `GUIDE_STUDIO` in `area-riservata.html`

## Passaggi

### 1. Posizionare il file HTML

Copiare il file `.html` della guida nella cartella principale del progetto (stessa cartella di `area-riservata.html`).

### 2. Aggiungere la voce nell'array GUIDE_STUDIO

In `area-riservata.html`, cercare l'array `GUIDE_STUDIO` (sezione JS "GUIDE DELLO STUDIO"). Aggiungere un oggetto con questi 4 campi:

```javascript
const GUIDE_STUDIO = [
  // guide esistenti...
  { titolo: 'Titolo della Guida', desc: 'Breve descrizione visibile nella card.', tag: 'Categoria', href: 'nome-file.html' }
];
```

| Campo    | Cosa inserire                                      |
|----------|----------------------------------------------------|
| `titolo` | Nome completo della guida                          |
| `desc`   | 1-2 righe di descrizione (appare sotto il titolo)  |
| `tag`    | Etichetta breve (es. Sicurezza, Assunzioni, Fisco) |
| `href`   | Nome esatto del file HTML nella root del progetto  |

### 3. Push su GitHub

Dopo aver aggiunto file + voce array, fare push su `main`. Vercel deploya automaticamente.

## Dove appare

Le guide sono visibili nella tab **Documenti** dell'area riservata, nella sezione "Guide dello Studio" sotto i documenti caricati dall'admin. Si aprono in una nuova scheda e sono accessibili a tutti i clienti loggati.

## Note

- Le guide NON passano dal database Supabase, sono link statici a file HTML
- Non serve caricare nulla dall'admin panel
- Il file HTML puo contenere il proprio CSS/stile (come `adempimenti-sicurezza.html`)
- I clienti possono stampare la guida direttamente dal browser
