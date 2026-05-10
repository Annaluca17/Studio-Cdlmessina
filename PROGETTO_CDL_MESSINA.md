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
| **Staging Vercel** | https://studio-cdlmessina-ftdb.vercel.app |
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
/* Delay stagger — hero usa d1→d4, sezioni usano d1→d3 */
.d1 { transition-delay:.1s } .d2 { transition-delay:.2s }
.d3 { transition-delay:.3s } .d4 { transition-delay:.4s }
```

---

## 4. Struttura File nel Repository

```
Studio-Cdlmessina/
├── index.html              ✅ LIVE — sito principale (aggiornato 2026-05-09)
├── area-riservata.html     ✅ LIVE — portale clienti
├── Logo_migliorato.png     ✅ LIVE — logo originale (sfondo bianco, usato come favicon/OG)
├── Logo_transparent.png    ✅ LIVE — logo con sfondo trasparente (nav + footer)
├── Paolo_Messina.png       ✅ LIVE — foto profilo Paolo Messina (sezione #chi-sono)
├── robots.txt              ✅ LIVE
├── sitemap.xml             ✅ LIVE
├── PROGETTO_CDL_MESSINA.md ✅ Project brief
├── privacy-policy.html     ✅ LIVE — informativa GDPR (creata 2026-05-09)
└── cookie-policy.html      ✅ LIVE — informativa cookie (creata 2026-05-09)
```

> **NOTA LOGO:** esistono due versioni del logo.
> - `Logo_migliorato.png` — sfondo bianco, usato per favicon, OG image, meta tag
> - `Logo_transparent.png` — sfondo trasparente, usato in `<nav>` e `<footer>` con CSS `filter:brightness(0) invert(1)` che lo rende bianco su sfondo navy
>
> **Non usare `Logo_migliorato.png` nella navbar/footer**: il filtro CSS renderebbe tutto bianco (logo invisibile).

---

## 5. index.html — Sito Principale

### Architettura
SPA con **sezioni ancora** (`#hero`, `#aziende`, `#privati`, `#chi-sono`, `#faq`, `#contatti`). Navigazione via scroll nativo + `scroll-margin-top` per compensare navbar fissa.

> **IMPORTANTE:** versione attuale usa anchor link `href="#sezione"` (non JS show/hide).  
> Versione precedente usava JS con `showView()` — quella è obsoleta, non usarla.

### Sezioni in ordine
1. **Hero** — eyebrow + nome studio + headline + sottotitolo + 2 card (Aziende / Privati)
2. **Servizi Aziende** (`#aziende`) — 3 card featured + 3 standard
3. **Strip Metriche Aziende** — navy, 100% / 24h / 360°
4. **Servizi Privati** (`#privati`) — 4 card (Conciliazione / CTP / Riconteggio / Previdenza)
5. **Strip Metriche Privati** — navy, 24h / 10+
6. **Chi Sono** (`#chi-sono`) — foto Paolo Messina + bio + tag competenze
7. **FAQ** (`#faq`) — accordion 5 domande
8. **Contatti** (`#contatti`) — info studio + form Formspree

### Hero — Struttura Elemento per Elemento
```html
<!-- Ordine esatto degli elementi nel hero, con relative classi reveal/delay -->
<div class="eyebrow reveal">          <!-- "Consulente del Lavoro · Paternò (CT)" -->
<p class="hero-studio reveal d1">     <!-- "Studio CDL Paolo Messina" — Cormorant Garamond -->
<h1 class="reveal d2">               <!-- Headline principale -->
<p class="hero-sub reveal d3">        <!-- Sottotitolo -->
<div class="hero-cards reveal d4">    <!-- 2 card Aziende / Privati -->
```

### CSS — Classe hero-studio
```css
.hero-studio {
  font-family: 'Cormorant Garamond', Georgia, serif;
  font-size: clamp(20px, 2.5vw, 32px);
  color: rgba(255,255,255,.78);
  letter-spacing: .04em;
  margin-bottom: 20px;
  font-weight: 400;
}
```

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
// ⚠️ NAMING CRITICO: NON usare "scrollTo" come nome funzione custom
// (conflitto con window.scrollTo nativo del browser)

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
4 "schermate" mostrate/nascoste via JS (classe `.active`):
- `#screen-login` — form login email+password
- `#screen-setpwd` — schermata "Imposta la tua password" (invite/recovery Supabase)
- `#screen-app` — dashboard con tab: Documenti / Moduli / Account / Admin ★
- `#screen-form` — modulo assunzione compilabile a schermo intero

Il JS rileva `type=invite` o `type=recovery` nell'URL hash di Supabase e mostra `screen-setpwd` prima dell'accesso alla dashboard.

### Supabase — Configurazione
```javascript
// Valori già impostati e verificati in area-riservata.html
const SUPABASE_URL  = 'https://zhrvcklhcbdzkgzlonbr.supabase.co';
const SUPABASE_ANON = 'eyJhbGci...CZk4'; // JWT anon key — verificata 2026-05-09
```

> ⚠️ **FREE TIER — PAUSA AUTOMATICA:** Supabase mette in pausa il progetto dopo 7 giorni di inattività. Alla prima richiesta dopo una pausa, il sito impiega 1-2 minuti a rispondere (status `COMING_UP`). Per evitarlo: fare almeno un accesso a settimana oppure passare al piano Pro ($25/mese).


**URL Configuration su Supabase (già fatto o da fare):**
```
Authentication → URL Configuration
Site URL:      https://studio-cdlmessina-ftdb.vercel.app
               (aggiornare a https://www.cdlmessina.it quando dominio attivo)
Redirect URLs: https://studio-cdlmessina-ftdb.vercel.app/area-riservata.html
               https://www.cdlmessina.it/area-riservata.html
```

### Database Supabase — Tabelle

#### `moduli_assunzione`
```sql
-- Colonne principali (schema completo):
id UUID PK, user_id UUID FK→auth.users, submitted_by TEXT, submitted_at TIMESTAMPTZ,
azienda, sede, cognome_nome, codice_fiscale, qualifica, livello, mansioni, note TEXT,
stipendio, superminimo, altre_voci NUMERIC, stipendio_note, superminimo_note, altre_note TEXT,
ore_totali, perc_parttime NUMERIC,
tempo_pieno, tempo_parziale, indeterminato, determinato, apprendistato BOOLEAN,
indet_dal, det_dal, det_al, appr_dal, data_firma DATE,
firma_datore, firma_lavoratore TEXT,
all_doc, all_cf, all_pds BOOLEAN,
file_doc_identita, file_codice_fiscale, file_permesso_soggiorno TEXT,
orario_jsonb JSONB
```
RLS: INSERT richiede `auth.uid() = user_id`; SELECT: utenti vedono propri, admin (`segreteria@cdlmessina.it`) vede tutti.

#### `documenti_cliente`
```sql
id UUID PK, user_id UUID FK→auth.users (NULL = broadcast a tutti),
nome TEXT, descrizione TEXT, file_path TEXT, tag TEXT, created_at TIMESTAMPTZ, created_by TEXT
```
RLS: clienti vedono documenti propri + broadcast (`user_id IS NULL`); admin inserisce, legge tutto, elimina.

#### Funzione `get_utenti()`
Funzione `SECURITY DEFINER` con `SET search_path = ''`. Restituisce `id, email, created_at` da `auth.users` solo se chiamata da `segreteria@cdlmessina.it`. EXECUTE revocato da `anon` e `PUBLIC`.

### Storage Supabase
```
Bucket: "documenti" → PRIVATE
Policies RLS su storage.objects:
  - INSERT: authenticated (upload allegati + documenti)
  - SELECT: authenticated (download via signed URL 300s)
  - DELETE: authenticated (admin elimina documenti)
Struttura cartelle:
  allegati/{email_sanitized}/         → allegati moduli assunzione
  condivisi/broadcast/                → documenti per tutti i clienti
  condivisi/clienti/{user_id}/        → documenti per singolo cliente
```

### Funzionalità Implementate
| Feature | Stato |
|---|---|
| Login email + password | ✅ Operativo |
| Logout | ✅ Operativo |
| Schermata "Imposta password" (invite/recovery) | ✅ Operativo |
| Cambio password autonomo (tab Account) | ✅ Operativo |
| Download documenti da DB (tab Documenti) | ✅ Operativo — query `documenti_cliente` con RLS |
| Modulo Assunzione compilabile online | ✅ Operativo — font aumentati, part-time %, tabella orario opzionale |
| Salvataggio modulo su database | ✅ Operativo — user_id, null cleanup, whitelist colonne |
| Upload allegati (doc identità, CF, permesso) | ✅ Operativo — file in Storage `allegati/{email}/` |
| Conferma invio modulo con overlay | ✅ Operativo — overlay a schermo intero con download PDF |
| Stampa/PDF del modulo compilato | ✅ Operativo (window.print() da overlay conferma) |
| Notifica email all'invio modulo | ✅ Operativo — POST a Formspree (solo notifica, non dati) |
| Pannello Admin ★ — moduli ricevuti | ✅ Operativo — tabella + modal dettaglio completo + download allegati |
| Pannello Admin ★ — upload documenti | ✅ Operativo — carica per singolo cliente o broadcast, elimina |
| Link "Area Clienti" nell'header pubblico | ✅ Operativo — nav desktop + menu mobile in index.html |

### Gestione Utenti e Documenti (Admin)
```
Creare cliente:   Supabase Dashboard → Authentication → Users → Invite user
                  → Cliente riceve email italiana → Imposta password → Accesso

Caricare documento per un cliente:
                  Area Riservata → Admin ★ → Carica Documento
                  → Scegli destinatario (singolo o "★ Tutti") → Upload

Visualizzare moduli ricevuti:
                  Area Riservata → Admin ★ → Moduli Ricevuti → Dettagli
                  (mostra tutti i campi + download allegati)

Reset password:   Dashboard Supabase → Users → ••• → Send magic link
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

Foto profilo: ✅ ATTIVA — file Paolo_Messina.png nel repo
              Implementazione nel DOM:
              <img src="Paolo_Messina.png"
                   alt="Paolo Messina — Consulente del Lavoro"
                   style="width:100%;height:100%;object-fit:cover;object-position:center top">
              Contenuto dentro .chi-photo (aspect-ratio 3/4 desktop e mobile, max-width:280px centrata su mobile)
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

| File | Descrizione | Stato |
|---|---|---|
| `Modulo Assunzione.html` | Template A4 stampabile | ✅ Convertito in form interattivo in area-riservata.html |
| `Adempimenti Sicurezza.html` | Template A4 landscape — D.Lgs. 81/08 | ❌ Da convertire (fase 2) |
| `Carta Intestata.html` | Template carta intestata studio | Documentale |
| `Email Signature.html` | Firma email | Documentale |

**Font documenti:** Quattrocento Sans — usato solo nei template A4, non nel sito web.

---

## 10. TODO — Priorità

### 🔴 Critico (P0)
- [x] **privacy-policy.html** — ✅ creata e deployata (2026-05-09)
- [x] **cookie-policy.html** — ✅ creata e deployata (2026-05-09)
- [x] **Logo visibile in navbar** — ✅ risolto con Logo_transparent.png (2026-05-09)
- [x] **Foto profilo Paolo Messina** — ✅ Paolo_Messina.png attivo in #chi-sono (2026-05-09)
- [x] **Nome Studio visibile nel hero** — ✅ aggiunto elemento .hero-studio (2026-05-09)
- [x] **Supabase ANON KEY** — ✅ verificata (2026-05-09)
- [x] **Tabella `moduli_assunzione`** — ✅ creata con RLS + tutte le colonne form (2026-05-09)
- [x] **Tabella `documenti_cliente`** — ✅ creata con RLS broadcast + per-utente (2026-05-10)
- [x] **Fix invio modulo** — ✅ aggiunto user_id al payload, null cleanup date/numeric, whitelist colonne (2026-05-09)
- [x] **Security linter Supabase** — ✅ rimossa utenti_view, hardened get_utenti(), revoke anon (2026-05-10)
- [ ] **Redirect URL Supabase** — aggiornare Site URL a `https://www.cdlmessina.it/area-riservata.html` quando dominio attivo
- [ ] **Email template italiano** — Auth → Email Templates → Invite user: tradurre in italiano
- [ ] **Leaked password protection** — Auth → Settings → Password Security → abilitare

### 🟡 Importante (P1)
- [x] **Link Area Clienti nell'header** — ✅ bottone gold nella nav desktop + menu mobile (2026-05-10)
- [x] **Collegamento Area Riservata nel footer** — ✅ (2026-05-09)
- [x] **Notifiche email a Paolo** — ✅ POST a Formspree dopo insert (2026-05-09)
- [x] **Pannello Admin completo** — ✅ moduli ricevuti con dettaglio + gestione documenti con upload/elimina (2026-05-10)
- [x] **Upload allegati modulo** — ✅ doc identità, CF, permesso soggiorno su Storage (2026-05-09)
- [x] **Conferma invio modulo** — ✅ overlay a schermo intero + download PDF (2026-05-10)
- [x] **Schermata imposta password** — ✅ screen-setpwd per flusso invite/recovery (2026-05-09)
- [ ] **Dominio cdlmessina.it** — configurare DNS su Vercel (Settings → Domains)

### 🟢 Fase 2 (priorità da definire)
- [ ] **Notifica email al cliente** — email automatica quando admin carica un nuovo documento
- [ ] **Upload documenti dal cliente** — cliente carica file (es. da firmare) visibili all'admin
- [ ] **Adempimenti Sicurezza** — convertire template A4 (D.Lgs. 81/08) in form compilabile

---

## 11. Istruzioni per Claude Code

### Come deployare una modifica
```bash
# Deploy automatico: push al branch main → Vercel pubblica in ~60 secondi
git add .
git commit -m "descrizione modifica"
git push origin main
```

### Dove mettere i file
```
Tutti i file HTML, CSS, JS, PNG → root del repository (stessa cartella di index.html)
Nessuna sottocartella (sito flat, no build step)
```

### Naming critico — errori da evitare
```javascript
// ❌ MAI usare "scrollTo" come nome di funzione custom
function scrollTo(id) { ... }    // SBAGLIATO — sovrascrive window.scrollTo

// ✅ Usare nome diverso
function scrollToSection(id) { ... }    // CORRETTO
```

### Pattern Supabase nell'area riservata
```javascript
const { createClient } = supabase;
const sb = createClient(SUPABASE_URL, SUPABASE_ANON);

sb.auth.onAuthStateChange((event, session) => { ... });

// Signed URL per download documento
const { data } = await sb.storage.from('documenti').createSignedUrl(filename, 60);
window.open(data.signedUrl, '_blank');

await sb.from('moduli_assunzione').insert([payload]);
await sb.auth.updateUser({ password: nuovaPassword });
```

### Aggiungere un nuovo documento per i clienti
```
Area Riservata → login come segreteria@cdlmessina.it → tab Admin ★
→ Sezione "Carica Documento per i Clienti"
→ Nome, Categoria, Destinatario ("★ Tutti" o email specifica), File
→ Clicca "Carica documento"
Il documento appare nella tab Documenti del cliente destinatario.
Per eliminare: bottone ✕ nella tabella documenti caricati.
```

### Aggiungere un nuovo cliente
```
Supabase Dashboard → Authentication → Users → Invite user
→ Email cliente → Supabase invia email (tradurre template in italiano!)
→ Cliente clicca link → area-riservata.html mostra "Imposta la tua password"
→ Login successivo: email + password
```

---

## 12. Decisioni Architetturali — Rationale

| Decisione | Alternativa scartata | Motivazione |
|---|---|---|
| Vanilla JS (no React/Vue) | React, Next.js | Zero build step, profilo no-code del gestore |
| Supabase | Firebase, Memberstack | Free tier 50 utenti, dashboard visuale, Storage integrato |
| Formspree per form pubblico | EmailJS, backend custom | Zero configurazione, free 50 msg/mese |
| SPA con anchor link | SPA con JS show/hide | SEO superiore, URL condivisibili, no bug naming |
| File flat nel repo root | Sottocartelle src/ | Compatibile Vercel senza config, semplicità no-code |
| Logo doppio (bianco + trasparente) | Logo unico | PNG bianco usato per OG/favicon; trasparente per nav/footer con filter CSS |

---

## 13. Contatti Progetto

| Ruolo | Nome | Contatto |
|---|---|---|
| Titolare Studio | Paolo Messina | paolo.messina@cdlmessina.it |
| Gestore Progetto / Repository | Luca (Annaluca17) | github.com/Annaluca17 |
| Architettura & Sviluppo | Claude (Anthropic) | claude.ai |

---

## 14. Prompt di Verifica Post-Deploy e Istruzioni GitHub

### 14a. File da caricare su GitHub dopo ogni sessione di lavoro

Aprire il repository https://github.com/Annaluca17/Studio-Cdlmessina e, nella root, caricare/sostituire i file elencati nella tabella seguente. Per ciascun file: clicca sul nome del file esistente → matita (Edit) → oppure trascina il nuovo file nella root se è un file nuovo.

**Sessione 2026-05-09 — file da caricare:**

| File | Azione | Note |
|---|---|---|
| `index.html` | **Sostituire** | Aggiornato: hero-studio, logo trasparente, foto Paolo |
| `Logo_transparent.png` | **Aggiungere** (nuovo) | Logo con sfondo trasparente per nav/footer |
| `Paolo_Messina.png` | **Aggiungere** (nuovo) | Foto profilo sezione Chi Sono |
| `PROGETTO_CDL_MESSINA.md` | **Sostituire** | Project brief aggiornato |

> Dopo l'upload fare commit su `main`. Vercel rileva il push e pubblica in ~60 secondi.  
> Verificare il deploy su: https://studio-cdlmessina-ftdb.vercel.app

---

### 14b. Prompt di Verifica — da incollare a Claude nella sessione successiva

Usa questo prompt all'inizio della prossima sessione per far verificare a Claude lo stato del sito live prima di procedere con nuove modifiche.

---

```
Leggi il file PROGETTO_CDL_MESSINA.md allegato a questo progetto prima di fare qualsiasi cosa.

Devi verificare lo stato attuale del sito live e confermare che le ultime modifiche deployate siano operative. Il sito è raggiungibile a: https://studio-cdlmessina-ftdb.vercel.app

Esegui le seguenti verifiche in sequenza usando gli strumenti a disposizione (Vercel MCP o web fetch):

**CHECKLIST DI VERIFICA — Sessione 2026-05-09**

1. LOGO NAVBAR
   - Controlla che nella `<nav>` il tag `<img>` punti a `Logo_transparent.png` (NON a `Logo_migliorato.png`)
   - Controlla che il CSS `.nav-logo img` abbia `filter:brightness(0) invert(1)`
   - Stato atteso: logo bianco visibile su sfondo navy

2. LOGO FOOTER
   - Controlla che nel `<footer>` il tag `<img>` punti a `Logo_transparent.png`
   - Stato atteso: logo bianco visibile su sfondo navy

3. HERO — NOME STUDIO
   - Controlla che nel `<section class="hero">` esista l'elemento `<p class="hero-studio reveal d1">Studio CDL Paolo Messina</p>`
   - Controlla che nel CSS esista la classe `.hero-studio` con `font-family: Cormorant Garamond`
   - Controlla che l'eyebrow dica "Consulente del Lavoro · Paternò (CT)" (NON "Studio di Consulenza del Lavoro · Paternò")
   - Stato atteso: nome studio visibile nel hero sopra il titolo principale

4. FOTO PROFILO
   - Controlla che nella sezione `#chi-sono` dentro `.chi-photo` ci sia un `<img src="Paolo_Messina.png" ...>`
   - Controlla che NON ci sia più il placeholder SVG (circle + path) né il testo "Foto Profilo"
   - Stato atteso: foto reale di Paolo Messina visibile

5. STAGGER ANIMAZIONI HERO
   - Verifica che l'ordine dei delay sia: eyebrow (nessuno), hero-studio (d1), h1 (d2), hero-sub (d3), hero-cards (d4)
   - Stato atteso: sequenza di animazioni corretta all'apertura del sito

Per ogni punto riporta: ✅ Confermato / ❌ Non trovato / ⚠️ Parziale — con dettaglio del codice rilevato.

Al termine della verifica, se ci sono anomalie, correggi direttamente il file index.html e fornisci la versione aggiornata da caricare su GitHub.
```

---

### 14c. Procedura GitHub Upload (passo-passo per utente non tecnico)

```
1. Vai su https://github.com/Annaluca17/Studio-Cdlmessina
2. Assicurati di essere nel branch "main" (visibile in alto a sinistra)

── PER SOSTITUIRE UN FILE ESISTENTE ──
3. Clicca sul nome del file (es. index.html)
4. Clicca sull'icona matita ✏️ in alto a destra ("Edit this file")
5. Seleziona tutto il testo (Ctrl+A) e cancella
6. Incolla il contenuto del nuovo file
7. Scorri in basso → "Commit changes"
8. Lascia il messaggio di default o scrivi una descrizione
9. Clicca "Commit changes" (bottone verde)

── PER AGGIUNGERE UN FILE NUOVO (es. immagini) ──
3. Clicca "Add file" → "Upload files"
4. Trascina il file PNG nella zona di upload
5. Scorri in basso → "Commit changes"
6. Clicca "Commit changes" (bottone verde)

── VERIFICA DEPLOY ──
7. Vai su https://vercel.com → il progetto mostrerà "Building..." per ~60 secondi
8. Quando compare "Ready", il sito è live
9. Apri https://studio-cdlmessina-ftdb.vercel.app e verifica visivamente
```

---

*Documento aggiornato in data 2026-05-10. Sessioni 09-10/05: sito pubblico completo (SEO, privacy, cookie), area riservata con pannello admin (moduli + documenti), sistema documenti da DB con upload/download/elimina, schermata imposta password, conferma invio con PDF, allegati modulo, link Area Clienti nell'header, fix security linter Supabase. Prossima milestone: configurazione dominio cdlmessina.it su Vercel, template email italiano su Supabase.*
