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
`<link>`. L'unica chiamata di rete è quella, opzionale, verso l'API di Claude
per la lettura degli screenshot (vedi sotto) — nessun tracking, nessun'altra
chiamata.

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

## Lettura screenshot via Claude API — idea principale dell'app

Pensata per l'uso da telefono, subito dopo la partita: tap su "📷 Leggi da
screenshot" apre la fotocamera (o il file picker su desktop, non è bloccato ma
il flusso è disegnato per il mobile), l'immagine viene inviata al modello
`claude-haiku-4-5-20251001` con un prompt che chiede di rispondere solo con
`VICTORY` / `DRAW` / `DEFEAT` / `UNKNOWN`, e il risultato viene proposto con un
tap di conferma — mai inserito automaticamente senza controllo.

**Chiamata diretta dal browser, senza backend.** La richiesta va da
`fetch()` direttamente a `https://api.anthropic.com/v1/messages`, con l'header
`anthropic-dangerous-direct-browser-access: true` che Anthropic richiede
apposta per questo caso d'uso. La documentazione ufficiale sconsiglia le
chiamate dirette da browser **in produzione multi-utente**, perché la chiave
sarebbe visibile a chiunque ispezioni il traffico del sito. Qui la situazione è
diversa: è un'app a singolo utente (solo Peppe), la chiave è quella personale
dell'utente stesso, inserita e salvata solo nel suo `localStorage`, e non
transita mai da un server terzo (nemmeno da me). Per questo caso — un file
statico, un solo utilizzatore, nessun backend possibile su GitHub Pages — è il
compromesso scelto consapevolmente, non una svista. Se in futuro l'app dovesse
avere più utenti o girare su un dominio condiviso, andrebbe rivista con un vero
backend/proxy che nasconda la chiave.

**Perché niente conferma automatica silenziosa:** un modello vision può
sbagliare lettura (schermata poco chiara, lingua diversa, foto storta). Il tap
di conferma resta sempre un passaggio umano — lo screenshot velocizza
l'inserimento, non lo sostituisce del tutto.

**Costo:** a carico dell'utente sulla propria chiave Anthropic, non
dell'app/del repository. `claude-haiku-4-5-20251001` è stato scelto perché è
il modello vision più economico disponibile per un compito così semplice
(classificazione in una parola).
