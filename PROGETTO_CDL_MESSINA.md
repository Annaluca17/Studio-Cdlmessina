# Studio CDL Paolo Messina — Project Brief per Claude Code

> **Scopo di questo documento:** fornire a Claude Code tutto il contesto necessario per implementare, correggere ed estendere il progetto senza iterazioni di chiarimento. Aggiornare questo file a ogni milestone completata.

---

## 1. Identità del Progetto

| Campo | Valore |
|---|---|
| **Studio** | Studio CDL Paolo Messina |
| **Titolare** | Paolo Messina — Consulente del Lavoro |
| **Indirizzo** | Via Somalia n.6, 95047 Paternò (CT) |
| **Telefono** | +39 392 60 24 447 |
| **Email pubblica** | segreteria@cdlmessina.it |
| **Email titolare** | paolo.messina@cdlmessina.it |
| **P.IVA** | 05799860878 |
| **Albo CDL** | n. 789 |
| **Sito live** | https://www.cdlmessina.it |
| **Staging Vercel** | https://studio-cdlmessina.vercel.app |
| **Repository GitHub** | https://github.com/Annaluca17/Studio-Cdlmessina |

---

## 2. Stack Tecnologico

```
Frontend:   HTML + CSS + Vanilla JS (no framework, no build step)
Hosting:    Vercel (deploy automatico da GitHub push)
Auth/DB:    Supabase (JS client via CDN)
Forms:      Formspree (form contatti pubblico) + Supabase (moduli area riservata)
DNS:        Dominio cdlmessina.it acquistato, puntare a Vercel
Repository: github.com/Annaluca17/Studio-Cdlmessina
```

### Dipendenze CDN (nessun package.json)
```html
<!-- Supabase -->
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

<!-- Google Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,400;0,500;0,600;1,400;1,500&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
```

---

## 3. Design System

### Palette Colori
```css
:root {
  --navy:     #0c1d3a;   /* nav, hero, footer, strip scure */
  --navy2:    #163660;   /* hover stati navy */
  --blue:     #1e6db5;   /* etichette sezione (sec-tag-text) */
  --gold:     #b89650;   /* accento primario: bordi, CTA, em, regole */
  --gold-dim: rgba(184,150,80,0.22);  /* gold tenue */
  --cream:    #f4f1ea;   /* bg sezione Privati */
  --offwhite: #faf9f7;   /* bg sezione Aziende, card surfaces */
  --white:    #ffffff;   /* bg principale */
  --text:     #1a1a18;   /* testo primario */
  --muted:    #6b6b6a;   /* testo secondario, label */
  --rule:     rgba(0,0,0,0.09); /* divisori, bordi */
}
```

### Tipografia
- **Display/Serif:** Cormorant Garamond (400, 500, 600 — regular + italic) → heading, hero, metriche, CTA
- **Body/Sans:** DM Sans (300, 400, 500) → corpo testo, label, form, nav
- **Fallback serif:** Georgia
- **Fallback sans:** system-ui

### Spaziatura
```
Padding sezione:   88px 5% (desktop) / 64px 5% (mobile)
Altezza nav:       62px (--nav-h)
Gap colonne:       72px
Gap card grid:     1px (crea hairline visibile sul background)
Padding interno card: 36px 30px
```

### Regole di Design
- **Zero border-radius** ovunque (angoli tutti a 90°)
- **Nessun box-shadow** — la separazione avviene tramite colore di sfondo + bordo
- **Nessuna immagine** in background (solo colori + gradiente radiale faint in hero)
- **Nessun sistema di icone** — si usano frecce testo (`→`, `↻`, `↓`) e punti gold 4×4px
- **Card featured** → `border-left: 3px solid var(--gold)`
- **Watermark hero** → `PM` in Cormorant Garamond, `rgba(255,255,255,.022)`, position absolute

### Animazioni
```css
/* Reveal on scroll — classe .reveal + .on aggiunta da IntersectionObserver */
.reveal { opacity:0; transform:translateY(20px); transition:opacity .65s ease,transform .65s ease }
.reveal.on { opacity:1; transform:none }
/* Delay stagger */
.d1 { transition-delay:.1s } .d2 { transition-delay:.2s }
.d3 { transition-delay:.3s } .d4 { transition-delay:.4s }
```

---

## 4. Struttura File nel Repository

```
Studio-Cdlmessina/
├── index.html              ✅ LIVE — sito principale (ristrutturato 2026-05-09)
├── area-riservata.html     ✅ LIVE — portale clienti
├── Logo_migliorato.png     ✅ LIVE — logo principale (sfondo bianco)
├── robots.txt              ✅ LIVE
├── sitemap.xml             ✅ LIVE
├── PROGETTO_CDL_MESSINA.md ✅ Project brief
├── privacy-policy.html     ✅ LIVE — informativa GDPR (creata 2026-05-09)
└── cookie-policy.html      ✅ LIVE — informativa cookie (creata 2026-05-09)
```

---

## 5. index.html — Sito Principale

### Architettura
SPA con **sezioni ancora** (`#hero`, `#aziende`, `#privati`, `#chi-sono`, `#faq`, `#contatti`). Navigazione via scroll nativo + `scroll-margin-top` per compensare navbar fissa.

> **IMPORTANTE:** versione attuale usa anchor link `href="#sezione"` (non JS show/hide).  
> Versione precedente usava JS con `showView()` — quella è obsoleta, non usarla.

### Sezioni in ordine
1. **Hero** — headline `"Consulenza del lavoro nuova. Aggiornata. Su misura."` + 2 card (Aziende / Privati)
2. **Servizi Aziende** (`#aziende`) — 3 card featured + 3 standard
3. **Strip Metriche Aziende** — navy, 100% / 24h / 360°
4. **Servizi Privati** (`#privati`) — 4 card (Conciliazione / CTP / Riconteggio / Previdenza)
5. **Strip Metriche Privati** — navy, 24h / 10+
6. **Chi Sono** (`#chi-sono`) — placeholder foto + bio Paolo Messina + tag competenze
7. **FAQ** (`#faq`) — accordion 5 domande
8. **Contatti** (`#contatti`) — info studio + form Formspree

### Form Contatti
```
Backend:    Formspree
Action URL: https://formspree.io/f/meevlvwk
Campi:      nome, cognome, email, telefono, tipo (select), dipendenti (condizionale Azienda),
            necessita (condizionale Privato), messaggio, gdpr (checkbox required)
```

### SEO Implementato
- Meta description, keywords, canonical
- Open Graph completo
- Twitter Card
- Geo meta tags (Paternò CT)
- Schema.org JSON-LD: ProfessionalService + Person + FAQPage + WebSite
- robots.txt + sitemap.xml

### JS — Funzioni Chiave
```javascript
// ⚠️ NAMING CRITICO: NON usare "scrollTo" come nome funzione
// (conflitto con window.scrollTo nativo del browser)
// La versione corrente usa anchor href="#id" — non c'è JS di scroll

revealObs      // IntersectionObserver per animazioni reveal
navObs         // IntersectionObserver per active state nella navbar
toggleMenu()   // hamburger mobile
// Conditional form fields via addEventListener('change') su #tipo-select
// FAQ accordion via addEventListener('click') su .faq-q
```

### Responsività
```
≤ 900px: chi-grid colonna singola, contact-sec colonna singola, strip flex-column
≤ 660px: navbar nascosta → hamburger, hero-cards griglia singola, fg griglia singola
```

---

## 6. area-riservata.html — Portale Clienti

### Architettura
3 "schermate" mostrate/nascosta via JS (classe `.active`):
- `#screen-login` — form login email+password
- `#screen-app` — dashboard con tab: Documenti / Moduli / Account
- `#screen-form` — modulo assunzione compilabile a schermo intero

### Supabase — Configurazione
```javascript
// VARIABILI DA SOSTITUIRE (righe iniziali del file)
const SUPABASE_URL  = 'https://zhrvcklhcbdzkgzlonbr.supabase.co';
const SUPABASE_ANON = 'eyXXX...'; // anon key da Settings → API
```

**URL Configuration su Supabase (già fatto o da fare):**
```
Authentication → URL Configuration
Site URL:      https://studio-cdlmessina.vercel.app
               (aggiornare a https://www.cdlmessina.it quando dominio attivo)
Redirect URLs: https://studio-cdlmessina.vercel.app/area-riservata.html
               https://www.cdlmessina.it/area-riservata.html
```

### Database Supabase — Tabelle

#### `moduli_assunzione` (già creata via SQL Editor)
```sql
create table moduli_assunzione (
  id uuid default gen_random_uuid() primary key,
  submitted_by text,           -- email del cliente
  submitted_at timestamptz,
  azienda text, sede text,
  cognome_nome text, codice_fiscale text,
  qualifica text, livello text, mansioni text,
  note text,
  stipendio numeric, superminimo numeric, altre_voci numeric,
  ore_totali numeric,
  tempo_pieno boolean, tempo_parziale boolean,
  indeterminato boolean, determinato boolean, apprendistato boolean,
  indet_dal date, det_dal date, det_al date, appr_dal date,
  firma_datore text, firma_lavoratore text, data_firma date,
  all_doc boolean, all_cf boolean, all_pds boolean
);
-- RLS attiva
alter table moduli_assunzione enable row level security;
create policy "insert own" on moduli_assunzione
  for insert to authenticated with check (submitted_by = auth.email());
create policy "select admin" on moduli_assunzione
  for select to authenticated using (true);
```

### Storage Supabase
```
Bucket: "documenti" → PRIVATE (non pubblico)
Accesso: signed URL con scadenza 60 secondi (createSignedUrl)
Gestione: Paolo carica PDF dalla dashboard Supabase → Storage → documenti
```

### Documenti — Array Configurabile
```javascript
// In area-riservata.html — da aggiornare aggiungendo righe
const DOCUMENTI = [
  { nome: 'Nome documento', file: 'nome-file.pdf', data: 'YYYY-MM-DD', tag: 'Categoria' },
];
// Il file deve esistere nel bucket "documenti" di Supabase Storage
```

### Funzionalità Implementate
| Feature | Stato |
|---|---|
| Login email + password | ✅ Operativo |
| Logout | ✅ Operativo |
| Cambio password autonomo | ✅ Operativo |
| Download documenti (PDF da Storage) | ✅ Operativo |
| Modulo Assunzione compilabile online | ✅ Operativo |
| Salvataggio modulo su database | ✅ Operativo |
| Stampa/PDF del modulo compilato | ✅ Operativo (window.print()) |
| Notifica email all'invio modulo | ❌ Da implementare (Supabase Webhooks → Formspree/Resend) |
| Upload documenti dal cliente | ❌ Da implementare (fase 2) |

### Gestione Utenti (lato admin Paolo)
```
Creare cliente:  Supabase Dashboard → Authentication → Users → Invite user
                 Inserire email cliente → Supabase invia email con link
Reset password:  Dashboard → Users → ••• → Send magic link
Visualizzare moduli ricevuti: Dashboard → Table Editor → moduli_assunzione
```

---

## 7. Contenuto — Chi Sono (Paolo Messina)

```
Ruolo:       Consulente del Lavoro iscritto all'Albo CDL n.789
Titolo bio:  "Labour Relations Specialist"

Esperienza 1:
  Ruolo:    Labour Relations Specialist / HR Specialist — Payroll & Relazioni Sindacali
  Azienda:  Gruppo GPI S.p.A. (multinazionale, settore healthcare IT)
  Sede:     Trento
  Periodo:  Febbraio 2016 – Presente (10+ anni)
  Attività: gestione presenze, paghe e contributi, adempimenti enti previdenziali,
            fondi previdenziali e assistenziali, relazioni sindacali

Esperienza 2:
  Ruolo:    Consulente del Lavoro (autonomo)
  Azienda:  Studio CDL Paolo Messina
  Sede:     Paternò (CT)
  Periodo:  Gennaio 2021 – Presente (5+ anni)

Tag competenze: Relazioni Industriali, HR Outsourcing, Payroll & Contributi,
                Contrattazione Collettiva, Fondi Previdenziali, Welfare Aziendale

Foto profilo: MANCANTE — placeholder SVG presente
              Quando disponibile: <img src="foto-profilo.jpg" style="width:100%;height:100%;object-fit:cover">
              Da inserire dentro .chi-photo al posto del placeholder SVG
```

---

## 8. Servizi — Mappa Completa

### Aziende (pagina #aziende)
| N. | Label | Titolo | Badge |
|---|---|---|---|
| 01 | HR Outsourcing | Esternalizzazione Ufficio HR | Servizio Chiave |
| 02 | Relazioni Industriali | Contrattazione di Secondo Livello | Servizio Chiave |
| 03 | Compliance HR | Regolamenti Aziendali | Servizio Chiave |
| 04 | Payroll | Gestione Paghe & Contributi | — |
| 05 | Welfare | Welfare Aziendale | — |
| 06 | Legal | Gestione Controversie | — |

### Privati (pagina #privati)
| Pill | Titolo |
|---|---|
| Conciliazione | Assistenza alle Conciliazioni Sindacali |
| Contenzioso | CTP — Consulenza Tecnica di Parte |
| Retribuzione | Riconteggio Differenze Retributive |
| Previdenza | Posizione Previdenziale & Pensione |

---

## 9. Moduli HTML (Design System)

Presenti nel repository ZIP `Studio_CDL_Messina_Design_System.zip`:

| File | Descrizione | Stato |
|---|---|---|
| `Modulo Assunzione.html` | Template A4 stampabile — Scheda per Assunzione Dipendente | ✅ Convertito in form interattivo in area-riservata.html |
| `Adempimenti Sicurezza.html` | Template A4 landscape — tabella adempimenti D.Lgs. 81/08 | ❌ Da convertire in form interattivo (fase 2) |
| `Carta Intestata.html` | Template carta intestata studio | Documentale |
| `Email Signature.html` | Firma email | Documentale |

**Font documenti:** Quattrocento Sans (presente in `/fonts/`) — usato solo nei template A4, non nel sito web.

---

## 10. TODO — Priorità

### 🔴 Critico (P0)
- [x] **privacy-policy.html** — ✅ creata e deployata (2026-05-09)
- [x] **cookie-policy.html** — ✅ creata e deployata (2026-05-09)
- [ ] **Supabase ANON KEY** — verificare che sia incollata correttamente in area-riservata.html
- [ ] **Redirect URL Supabase** — impostare a `https://www.cdlmessina.it/area-riservata.html` quando dominio attivo

### 🟡 Importante (P1)
- [ ] **Foto profilo Paolo Messina** — caricare `foto-profilo.jpg` nel repo e sostituire placeholder SVG in `#chi-sono`
- [x] **Collegamento Area Riservata** — ✅ link aggiunto nel footer di index.html (2026-05-09)
- [ ] **Dominio cdlmessina.it** — configurare DNS su Vercel (Settings → Domains)
- [ ] **Notifiche email** — Supabase Database Webhooks → trigger su insert in `moduli_assunzione` → POST a Formspree o Resend

### 🟢 Fase 2
- [ ] **Adempimenti Sicurezza** — convertire template A4 in form compilabile (stesso pattern di Modulo Assunzione)
- [ ] **Upload documenti dal cliente** — Storage bucket "uploads-clienti" con RLS per-utente
- [ ] **Pannello admin** — pagina `admin.html` protetta per Paolo: visualizza moduli ricevuti, carica documenti, gestisce clienti senza accedere a Supabase Dashboard
- [ ] **Notifiche** — email automatica al cliente quando Paolo carica un nuovo documento

---

## 11. Istruzioni per Claude Code

### Come deployare una modifica
```bash
# Il deploy è automatico: qualsiasi push al branch main di GitHub
# viene rilevato da Vercel e pubblicato in ~60 secondi
git add .
git commit -m "descrizione modifica"
git push origin main
```

### Dove mettere i file
```
Tutti i file HTML, CSS, JS, PNG → root del repository (stessa cartella di index.html)
Nessuna sottocartella necessaria (sito è flat, no build step)
```

### Naming critico — errori da evitare
```javascript
// ❌ MAI usare "scrollTo" come nome di funzione custom
// (sovrascrive window.scrollTo e rompe la navigazione)
function scrollTo(id) { ... }    // SBAGLIATO

// ✅ Usare nome diverso
function scrollToSection(id) { ... }    // CORRETTO
```

### Pattern Supabase nell'area riservata
```javascript
// Init client (già presente nel file)
const { createClient } = supabase;
const sb = createClient(SUPABASE_URL, SUPABASE_ANON);

// Auth listener (gestisce login/logout automaticamente)
sb.auth.onAuthStateChange((event, session) => { ... });

// Download documento con URL firmato
const { data } = await sb.storage.from('documenti').createSignedUrl(filename, 60);
window.open(data.signedUrl, '_blank');

// Invio modulo a database
await sb.from('moduli_assunzione').insert([payload]);

// Cambio password
await sb.auth.updateUser({ password: nuovaPassword });
```

### Aggiungere un nuovo documento scaricabile
1. Caricare il PDF in Supabase Storage → bucket `documenti`
2. In `area-riservata.html`, trovare l'array `DOCUMENTI` (prime righe del `<script>`)
3. Aggiungere una riga:
```javascript
{ nome: 'Titolo documento', file: 'nome-file.pdf', data: 'YYYY-MM-DD', tag: 'Categoria' }
```

### Aggiungere un nuovo cliente
```
Supabase Dashboard → Authentication → Users → Invite user
→ Inserire email del cliente
→ Supabase invia email con link (il link punta a Redirect URL configurato)
→ Cliente imposta la password
→ Dal login successivo può usare email + password
```

---

## 12. Decisioni Architetturali — Rationale

| Decisione | Alternativa scartata | Motivazione |
|---|---|---|
| Vanilla JS (no React/Vue) | React, Next.js | Zero build step, deploy immediato su GitHub Pages/Vercel, profilo no-code del gestore |
| Supabase | Firebase, Memberstack | Free tier generoso per 50 utenti, dashboard visuale per admin, Storage integrato, JS client CDN |
| Formspree per form pubblico | EmailJS, backend custom | Zero configurazione, free fino 50 msg/mese, filtro spam incluso |
| SPA con anchor link | SPA con JS show/hide | SEO superiore (sezioni indicizzabili), URL condivisibili, nessun bug di naming function |
| File flat nel repo root | Sottocartelle src/ | Compatibile con Vercel senza configurazione, semplicità per gestore no-code |
| Vercel | GitHub Pages, Netlify | Già configurato, supporta serverless functions per future espansioni, deploy automatico |

---

## 13. Contatti Progetto

| Ruolo | Nome | Contatto |
|---|---|---|
| Titolare Studio | Paolo Messina | paolo.messina@cdlmessina.it |
| Gestore Progetto / Repository | Luca (Annaluca17) | github.com/Annaluca17 |
| Architettura & Sviluppo | Claude (Anthropic) | claude.ai |

---

*Documento aggiornato in data 2026-05-09. Nome repository corretto: Studio-Cdlmessina. Aggiornare a ogni milestone completata.*
