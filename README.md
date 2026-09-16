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
7. "+ Aggiungi" nello "Storico partite dettagliato" apre un form dove inserisci
   il punteggio (obbligatorio — da lì l'app calcola da sola Vittoria/Pareggio/
   Sconfitta) e tutte le statistiche della schermata di fine partita che vuoi
   tracciare (possesso, tiri, xG, passaggi, contrasti, parate, falli, cartellini,
   più le tre percentuali di dribbling/tiro/passaggio) — tutto opzionale tranne
   il punteggio. Ogni partita inserita così diventa una card cliccabile: in
   anteprima vedi data e punteggio, toccandola si apre la tabella completa.

**Giorno di gioco:** una partita giocata dopo mezzanotte ma prima delle 5:00 del
mattino conta ancora come il giorno precedente (storico giornaliero, settimana
Rivals e weekend Champions inclusi) — pensato per chi gioca a cavallo della
mezzanotte.

**Importante:** i dati vivono in `localStorage`, cioè restano solo sul browser e
dispositivo con cui li inserisci. Non c'è sincronizzazione tra telefono e PC, né
backup automatico — vedi `NOTES.md` per il perché di questa scelta.

**Uso multi-utente:** proprio perché i dati restano sul dispositivo, questo
stesso link (`https://pesix97.github.io/fut-tracker/`) può essere usato da più
persone senza alcun conflitto — ognuno lo apre dal proprio telefono e ha il
proprio storico Rivals/FUT Champions, completamente separato da quello degli
altri. Non serve login né configurazione: basta aprire il link su un altro
telefono per avere un tracker "vuoto" e indipendente.

## Stato di avanzamento

- [x] v1 — tracker base: tap V/P/S per Rivals e FUT Champions, storico giornaliero
      in localStorage, raggruppamento automatico per settimana (Rivals) e weekend
      (FUT Champions)
- [x] v2 — confronto tra settimane/weekend passati
- [~] lettura risultato da screenshot via Claude API — provata e poi rimossa
      (richiedeva una chiave API a pagamento); vedi `NOTES.md` per i dettagli e
      le alternative valutate
- [x] v3 — inserimento manuale delle statistiche di partita (form con punteggio
      + tabella completa), storico partite dettagliato con card cliccabili,
      giorno di gioco esteso fino alle 5:00
- [x] restyling UI — tab a slider, card e modale animate, header sticky con
      blur, transizioni fluide ovunque (nessun cambiamento di dati/funzioni)
- [ ] prossimo passo: lettura automatica della tabella statistiche da uno
      screenshot della schermata "SUMMARY" di fine partita, per pre-compilare
      questo stesso form — vedi `NOTES.md` (in corso, verificata la fattibilità
      con un test OCR reale)

## Struttura del progetto

- `docs/index.html` — l'app (pubblicata via GitHub Pages dalla cartella `/docs`)
- `README.md` — questo file
- `NOTES.md` — decisioni tecniche e note di sviluppo
