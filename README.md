# Cosa Guardiamo

Un'app per scegliere il film della serata.

- **Da solo:** cinque domande (anche a scelta multipla) e almeno 20 proposte su misura, da un catalogo di 647 film con indicazione di dove guardarli in Italia.
- **In gruppo:** chi organizza crea una serata con un codice, gli amici entrano con il link e un soprannome (nessuna registrazione), tutti votano sì o no su 30 film. Appena un film ha un sì da tutti, la votazione si ferma ed esce il film con dove vederlo.

È un unico file `index.html` più un database gratuito su Supabase per le serate di gruppo.

## Cosa c'è nella cartella

| File | A cosa serve |
|---|---|
| `index.html` | L'app intera |
| `catalogo.json` | Film aggiunti ogni settimana dall'aggiornamento automatico (all'inizio è vuoto) |
| `GUIDA-DATABASE.md` | Come creare e configurare il database su Supabase |
| `supabase/setup.sql` | Script per tabelle, regole di sicurezza e tempo reale |
| `supabase/pulizia-automatica.sql` | Cancella da solo serate e utenti anonimi vecchi |
| `.github/workflows/tieni-sveglio.yml` | Evita che il progetto Supabase gratuito vada in pausa |

## Pubblicare su GitHub Pages

1. **Prepara il database** seguendo `GUIDA-DATABASE.md`.
2. **Inserisci i dati di Supabase** in `index.html`: in cima allo script trovi
   ```js
   const SUPABASE_URL = "INSERISCI_QUI_IL_PROJECT_URL";
   const SUPABASE_KEY = "INSERISCI_QUI_LA_CHIAVE_PUBBLICA";
   ```
   Sostituisci i due testi con il Project URL e la chiave pubblica (publishable/anon). Mai la secret key.
3. **Crea il repository** su github.com: **New repository**, nome per esempio `cosa-guardiamo`, visibilità **Public** (con l'account gratuito Pages funziona solo sui repository pubblici).
4. **Carica i file**: nella pagina del repository, **Add file → Upload files**, trascina tutto il contenuto della cartella e conferma con **Commit changes**.
   - La cartella `.github` su Mac e Windows può essere nascosta. Se non viene caricata: nel repository vai su **Add file → Create new file**, scrivi come nome `.github/workflows/tieni-sveglio.yml` e incolla il contenuto del file.
   - La cartella `build` non serve: contiene solo la prova automatica.
5. **Attiva Pages**: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, branch `main`, cartella `/ (root)`, **Save**.
6. Dopo un minuto il sito è online su `https://TUO-NOME-UTENTE.github.io/cosa-guardiamo/`.
7. **Aggiungi i secrets** per il controllo anti-pausa: **Settings → Secrets and variables → Actions → New repository secret**, crea `SUPABASE_URL` e `SUPABASE_KEY`. Poi in **Actions → Tieni sveglio Supabase → Run workflow** puoi provarlo subito.

## Come si usa in gruppo

1. Scrivi il tuo soprannome, tocca **Serata di gruppo** e rispondi al quiz: l'app sceglie 30 film adatti.
2. Tocca **Invia l'invito** e mandalo nel gruppo WhatsApp: il link contiene già il codice.
3. Gli amici aprono il link, scrivono un soprannome e toccano **Entra**.
4. Quando ci siete tutti, avvia la votazione. Si vota trascinando la locandina o con i pulsanti.
5. Appena un film ha un sì da tutti, esce su tutti i telefoni. Se nessun film è unanime, vince quello con più sì.

## Da sapere

- **Sito pubblico:** chiunque abbia il link può usare l'app. Le serate però le vede solo chi ci è entrato con il codice.
- **Pausa del progetto gratuito:** Supabase mette in pausa i progetti senza attività per 7 giorni; il workflow `tieni-sveglio` lo evita. GitHub sospende i workflow programmati nei repository senza modifiche da 60 giorni: se succede, riattivalo da **Actions**. Se il progetto andasse comunque in pausa, si riattiva dalla dashboard di Supabase.
- **Dove guardarlo:** le piattaforme indicate sono state verificate a ottobre 2026; il pulsante JustWatch mostra sempre la disponibilità del giorno.
- **Catalogo:** per aggiungere film a mano, aggiungi un blocco in `catalogo.json`:
  ```json
  { "data": "2026-10-23", "films": [
    ["Titolo italiano", "Titolo originale o vuoto", 2026, "Regia", 120, "RE", 0, 1, "N", "Trama in una frase.", 1]
  ] }
  ```
  Ordine dei campi: titolo, titolo originale, anno, regia, minuti (0 se non noti), umori (R ridere, E emozionarsi, A adrenalina, B brivido, P pensare, S evasione, L romantico), adatto alle famiglie (1/0), notorietà (0-2), piattaforma (N Netflix, P Prime Video, D Disney+, H HBO Max, A Apple TV+, oppure vuoto), trama, disponibilità verificata (1/0).
