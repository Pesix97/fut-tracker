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
`<link>`. Nessuna chiamata di rete, nessun tracking.

**Limite noto e voluto:** essendo `localStorage`, i dati non si sincronizzano tra
browser o dispositivi diversi. Va bene per un tabellone personale rapido; se in
futuro servisse condividerlo (es. tra più membri del club) andrebbe ripensato
con uno storage condiviso, che oggi non c'è.

## Idea futura: lettura screenshot via Claude API

Invece del tap manuale dopo ogni partita, si potrebbe caricare lo screenshot
della schermata di fine match (EA mostra già W/D/L, risultato e modalità) e
farlo leggere a un modello Claude via API per compilare automaticamente il tap.
Non implementato: richiederebbe una chiamata di rete e quindi una chiave API da
gestire, in un progetto che oggi è volutamente senza backend. Da valutare più
avanti se il tap manuale diventa scomodo con volumi alti di partite.
