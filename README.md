# FUT Tracker

Tabellone risultati per il proprio club **EA Sports FC 27 Ultimate Team**. App web
a file singolo (HTML/CSS/JS), nessun backend: un tap per registrare Vittoria, Pareggio
o Sconfitta in **Rivals** e in **FUT Champions**, con storico giornaliero.

App live: https://pesix97.github.io/fut-tracker/

## Come si usa

1. Apri l'URL sopra (funziona anche da telefono — su iOS/Android si può aggiungere
   alla schermata Home per aprirla come un'app).
2. Scegli la tab **Rivals** o **FUT Champions**.
3. Dopo ogni partita, tap su Vittoria / Pareggio / Sconfitta.
4. Le statistiche (conteggio e barra percentuale) e lo storico giorno per giorno si
   aggiornano subito.
5. "Annulla ultimo tap" corregge un tap sbagliato; "Reset" in alto azzera tutto lo
   storico salvato su quel browser.

**Importante:** i dati vivono in `localStorage`, cioè restano solo sul browser e
dispositivo con cui li inserisci. Non c'è sincronizzazione tra telefono e PC, né
backup automatico — vedi `NOTES.md` per il perché di questa scelta.

## Stato di avanzamento

- [x] v1 — tracker base: tap V/P/S per Rivals e FUT Champions, storico giornaliero
      in localStorage, raggruppamento automatico per settimana (Rivals) e weekend
      (FUT Champions)
- [ ] idea futura: inserimento automatico dei risultati leggendo uno screenshot di
      fine partita tramite Claude API, invece del tap manuale (vedi `NOTES.md`)

## Struttura del progetto

- `docs/index.html` — l'app (pubblicata via GitHub Pages dalla cartella `/docs`)
- `README.md` — questo file
- `NOTES.md` — decisioni tecniche e note di sviluppo
