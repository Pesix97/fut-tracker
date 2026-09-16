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

## Idea futura: dati di partita oltre al risultato

Oltre a V/P/S, si è parlato di registrare anche le statistiche della singola
partita (tiri, passaggi, contrasti ecc.), probabilmente con inserimento
manuale via un piccolo form invece che un tap singolo. Non ancora progettata:
va pensata la UI (un form per partita è più lento di un tap, quindi va capito
quanto dettaglio serve davvero) e se aggiungerla come dato opzionale per
partita nello stesso `localStorage.futTracker.v1`, senza appesantire il flusso
rapido attuale per chi vuole solo il risultato.
