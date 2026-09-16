# Note tecniche

## Cosa NON fa (di proposito)

Il tracker registra solo i risultati delle partite (Rivals e FUT Champions).
Niente gestione rosa, niente monete, niente mercato/transfer. Tenerlo mirato a
una cosa sola lo rende un file HTML statico senza stato server, senza login e
senza rischio di esporre dati di gioco sensibili — un tabellone tap-and-go, non
un club manager.

## Perché GitHub Pages e non Netlify

Il progetto è un singolo file statico, senza build step e senza necessità delle
funzioni serverless/edge di Netlify (usate invece per `f1-26-setup-companion`,
che è un progetto diverso e più complesso). Per un caso così semplice, GitHub
Pages dà un URL pubblico stabile direttamente dallo stesso repository dove vive
il codice, senza un servizio esterno da collegare — stesso approccio già usato
per `lentoni-dashboard`.

## Dati e storage

Tutto lo storico è un unico oggetto in `localStorage` sotto la chiave
`futTracker.v1`, con un array di partite `{ mode, result, day, period, ts }`:

- `mode`: `"rivals"` o `"champions"`
- `result`: `"W"` / `"D"` / `"L"`
- `day`: data in formato `YYYY-MM-DD`, usata per lo storico giornaliero
- `period`: settimana ISO (`YYYY-Www`) per Rivals, oppure la data del venerdì di
  riferimento per il weekend di FUT Champions — usato per aggregare le
  statistiche "correnti" in cima alla pagina
- `ts`: timestamp, usato per l'ordine e per "annulla ultimo tap"

Nessuna dipendenza esterna a parte i Google Fonts (Rajdhani + Inter) caricati via
`<link>`. Nessuna chiamata di rete, nessun tracking (era stata introdotta una
chiamata all'API di Claude per la lettura degli screenshot, poi rimossa — vedi
sotto).

**Limite noto e voluto:** essendo `localStorage`, i dati non si sincronizzano tra
browser o dispositivi diversi. Va bene per un tabellone personale rapido; se in
futuro servisse condividerlo (es. tra più membri del club) andrebbe ripensato
con uno storage condiviso, che oggi non c'è.

## Confronto tra periodi

`renderComparison()` raggruppa le partite per `period` (settimana ISO per
Rivals, venerdì di riferimento per FUT Champions — stesso campo già usato per
le statistiche "correnti") e mostra fino alle ultime 12 righe, più recenti in
cima, con la barra W/D/L e il periodo attuale evidenziato. Nessun nuovo campo
dati: usa lo stesso schema già salvato in `localStorage`.

## Lettura screenshot via Claude API — provata e rimossa (16/09/2026)

Era l'idea principale dell'app: tap su "📷 Leggi da screenshot", immagine
inviata al modello `claude-haiku-4-5-20251001` via `fetch()` diretto dal
browser a `https://api.anthropic.com/v1/messages` (header
`anthropic-dangerous-direct-browser-access: true`, chiave personale salvata
solo in `localStorage`), risultato proposto con un tap di conferma.

Implementata e funzionante, poi **rimossa su richiesta esplicita**: richiede
una chiave API Anthropic a consumo, e per ora si preferisce restare sul tap
manuale, senza costi. Il codice non è più nel repository (rimosso nel commit
successivo alla v2) — si può recuperare dallo storico Git se servisse
reintrodurla.

**Se in futuro si volesse automatizzare la lettura senza spesa**, l'alternativa
è OCR client-side con **Tesseract.js**: gira interamente nel browser, nessuna
chiave, nessun costo, nessuna chiamata di rete. Il compromesso è
l'affidabilità: un OCR generico legge peggio di un modello vision su schermate
con glow, filtri colore o testo in font stilizzati tipici di EA FC — andrebbe
provato sul serio prima di fidarcisi, mentre un token registra sempre esatto.
Da valutare se il tap manuale dovesse diventare scomodo con volumi alti di
partite.

## v3 — statistiche di partita: form manuale + storico dettagliato (16/09/2026)

Implementato l'inserimento delle statistiche della schermata "SUMMARY" di fine
partita (quella con possesso, tiri, xG, passaggi, contrasti, parate, falli,
cartellini più le tre percentuali a cerchio di dribbling/tiro/passaggio).

**Schema dati:** ogni partita in `state.matches` può avere un campo opzionale
`stats`: `{ scoreFor, scoreAgainst, possession:[tu,avv], shots:[tu,avv], ... }`
— un array `[tu, avversario]` per ogni statistica, più i due punteggi. Il
risultato (V/P/S) non si inserisce a parte: si calcola dal confronto
`scoreFor`/`scoreAgainst`, così non può mai essere in contraddizione con il
punteggio. Le partite con `stats` restano comunque dentro lo stesso array
`matches` usato dai tap semplici — contano regolarmente nei contatori
settimana/weekend e nel confronto tra periodi, e in più alimentano una sezione
a parte, "Storico partite dettagliato": una card per partita, cliccabile,
anteprima con data e punteggio, tap per aprire la tabella completa. Tutti i
campi statistica sono opzionali (solo il punteggio è obbligatorio) — un campo
lasciato vuoto si mostra come "—", non blocca il salvataggio.

**Perché il form manuale prima della lettura automatica:** è la base su cui si
aggancerà la lettura da screenshot (vedi sotto) — lo stesso form, semplicemente
pre-compilato invece che vuoto — ma è già uno strumento completo e funzionante
da solo, coerente con l'alternativa "a mano" discussa quando si è deciso di
togliere la lettura via Claude API.

**Giorno di gioco fino alle 5:00.** Peppe può giocare a cavallo della
mezzanotte; senza correzione, una partita delle 01:00 finirebbe nel giorno
sbagliato (storico giornaliero, settimana Rivals, weekend Champions). Aggiunta
`gamingNow()`: se l'ora locale è prima delle 5:00, si usa il giorno precedente
per calcolare `day` e `period`. Non tocca `ts` (resta il timestamp reale, usato
solo per l'ordinamento).

## Restyling UI (16/09/2026)

Nessun cambiamento di dati o funzionalità: solo interfaccia. La UI era
"rudimentale" (stati istantanei, nessuna transizione) — sistemato con:

- **Tab a slider**: l'indicatore verde dietro "Rivals"/"FUT Champions" ora
  scorre (misurato via `getBoundingClientRect`, non percentuali fisse) invece
  di scattare da uno stato all'altro.
- **Card partita dettagliata**: apertura/chiusura animata con la tecnica
  `grid-template-rows: 0fr → 1fr` (transizione fluida di un'altezza
  "automatica", cosa che un semplice `max-height` non permette in modo pulito).
- **Modale "+ Aggiungi"**: non più mostra/nascondi istantaneo, ma
  fade + slide-up con `backdrop-filter: blur()` dietro.
- **Header sticky con blur** durante lo scroll, barre statistiche e barre di
  confronto animate su `width`, micro-animazioni di ingresso per righe di
  storico/card/confronto, focus visibile sugli input del form.

Tutto CSS/poche righe JS di supporto (riposizionamento indicatore tab);
nessuna modifica allo schema `localStorage` né alla logica di calcolo.

## In corso: lettura automatica da screenshot (calibrazione in attesa)

Confermato con Peppe: ogni screenshot genera una partita **autosufficiente**
(non serve un tap precedente da agganciare — ambiguo con più vittorie nello
stesso giorno), caricato sempre lo stesso giorno in cui si gioca (niente
selettore data), schermata di riferimento è sempre e solo la tab "SUMMARY"
della schermata di fine partita.

**Test OCR fatto su uno screenshot reale (di esempio, non ancora quello di
Peppe — in attesa)**, con Tesseract (motore di `Tesseract.js`, testato qui
lato server solo per validare l'approccio prima di implementarlo nel browser):

- La tabella centrale (15 righe, 30 numeri) si legge quasi perfettamente con
  un ritaglio dell'area tabella + ingrandimento 3x: **29 valori su 30 esatti**
  in un solo test. Gratis, nessuna chiave, nessuna chiamata di rete.
- Le 3 percentuali per lato dentro i cerchietti (dribbling/tiro/passaggio,
  6 numeri totali) **non si leggono in modo affidabile**, nemmeno dopo vari
  tentativi di ritaglio più preciso e binarizzazione: il numero dentro
  l'anello colorato confonde ripetutamente il motore OCR. Per questi 6 numeri
  resta l'inserimento a mano nel form (già pronto, vedi sopra).

**Da fare quando arriva lo screenshot vero di Peppe:**
1. Calibrare le coordinate di ritaglio in **percentuale** (non pixel fissi),
   così funzionano a qualunque risoluzione 16:9 — i suoi screenshot sono
   sempre catture dirette dalla console, mai foto allo schermo, quindi il
   layout resta sempre identico.
2. Integrare `Tesseract.js` (libreria + WASM + dati lingua, caricati da CDN al
   primo utilizzo, poi in cache) — è una dipendenza esterna vera e propria,
   diversa dalla chiamata `fetch` leggera della versione Claude API: va
   documentata come tale quando la aggiungiamo.
3. Il flusso finale: carica screenshot → OCR sulla tabella (pre-compila il
   form) → i 3×2 valori dei cerchietti restano da inserire a mano → stessa
   schermata di verifica del form manuale, editabile, prima di salvare.

**Perché tutto questo:** i dati raccolti (con o senza OCR) sono pensati come
base per analisi e consigli di gioco futuri, non solo come archivio — da qui
l'importanza di uno schema pulito fin da subito, anche prima che l'analisi
vera e propria venga costruita.
