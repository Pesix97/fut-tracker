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
6. La card "Confronto settimane" (Rivals) / "Confronto weekend" (FUT Champions)
   mostra fino alle ultime 12 settimane/weekend a confronto, con la settimana in
   corso evidenziata.
7. In alternativa al tap manuale, "📷 Leggi da screenshot" legge il risultato
   direttamente da uno screenshot della schermata di fine partita — pensata per
   l'uso da telefono, subito dopo la partita. Serve una chiave API Claude
   personale, da impostare una volta sola in ⚙ Impostazioni (vedi sotto).

**Importante:** i dati vivono in `localStorage`, cioè restano solo sul browser e
dispositivo con cui li inserisci. Non c'è sincronizzazione tra telefono e PC, né
backup automatico — vedi `NOTES.md` per il perché di questa scelta.

## Lettura screenshot (setup)

1. Crea una chiave API su console.anthropic.com → API Keys.
2. In app, tocca ⚙ in alto e incolla la chiave in "Chiave API Claude" → Salva.
3. Da quel momento, il pulsante "📷 Leggi da screenshot" apre la fotocamera (su
   telefono) o il file picker, invia l'immagine a Claude e propone il risultato
   rilevato da confermare con un tap.

La chiave resta solo nel `localStorage` di quel browser e viene inviata solo ad
`api.anthropic.com` per analizzare l'immagine — vedi `NOTES.md` per i dettagli e
i limiti di questo approccio.

## Stato di avanzamento

- [x] v1 — tracker base: tap V/P/S per Rivals e FUT Champions, storico giornaliero
      in localStorage, raggruppamento automatico per settimana (Rivals) e weekend
      (FUT Champions)
- [x] v2 — confronto tra settimane/weekend passati; lettura del risultato da
      screenshot tramite Claude API (uso pensato per telefono)

## Struttura del progetto

- `docs/index.html` — l'app (pubblicata via GitHub Pages dalla cartella `/docs`)
- `README.md` — questo file
- `NOTES.md` — decisioni tecniche e note di sviluppo
