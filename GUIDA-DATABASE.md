# Cosa Guardiamo · configurare il database su Supabase

Tempo: circa 10 minuti. Serve solo per le **serate di gruppo**: il quiz da soli funziona anche senza.

## 1. Crea il progetto

1. Vai su **supabase.com** e registrati (va bene anche con l'account GitHub).
2. Clicca **New project**.
   - **Name:** `cosa-guardiamo`
   - **Database Password:** generane una e salvala da qualche parte (all'app non serve, ma ti servirà se un giorno vuoi collegarti al database).
   - **Region:** scegli una regione europea, per esempio *Central EU (Frankfurt)*: è la più vicina all'Italia.
   - Piano **Free**.
3. Aspetta un paio di minuti che il progetto sia pronto.

## 2. Attiva l'accesso anonimo

Serve perché gli amici possano entrare senza email né password.

1. Nel menu a sinistra: **Authentication → Sign In / Providers** (in alcune versioni: *Authentication → Settings*).
2. Attiva **Allow anonymous sign-ins** e salva.

## 3. Crea tabelle e regole

1. Nel menu a sinistra: **SQL Editor → New query**.
2. Apri il file `supabase/setup.sql`, copia **tutto** il contenuto e incollalo.
3. Clicca **Run**. Deve comparire *Success. No rows returned*.

Lo script crea:
- la tabella delle **serate** (codice, lista dei 30 film, stato, film scelto);
- la tabella dei **partecipanti** (soprannome e voti);
- le **regole di sicurezza**: ognuno vede solo le serate in cui è entrato e può cambiare solo i propri voti;
- il **controllo del film vincente**, fatto dal database dopo ogni voto, così il risultato è lo stesso per tutti;
- l'attivazione del **tempo reale** per voti e serate.

Puoi rieseguirlo quando vuoi: non cancella i dati.

## 4. Pulizia automatica (consigliata)

1. **SQL Editor → New query**, incolla `supabase/pulizia-automatica.sql` e clicca **Run**.
2. Se compare un errore su `pg_cron`: vai in **Database → Extensions**, cerca **pg_cron**, attivalo e riesegui.

Ogni notte cancella le serate più vecchie di 3 giorni, e ogni settimana gli utenti anonimi con più di 30 giorni. Così il database resta piccolo.

## 5. Copia le due informazioni per l'app

Vai in **Project Settings → API** (in alcune versioni: *Project Settings → Data API* e *API Keys*) e copia:

- **Project URL**, tipo `https://abcdefghijkl.supabase.co`
- la **chiave pubblica**: si chiama *publishable key* (inizia con `sb_publishable_`) oppure, nei progetti più vecchi, *anon public key* (inizia con `eyJ`).

Queste due vanno in cima al file `index.html`, nelle righe `SUPABASE_URL` e `SUPABASE_KEY`.

> La chiave pubblica è fatta per stare nel codice di un sito: può stare su GitHub. La **secret key** / **service_role key** invece **non va mai** messa nell'app né su GitHub.

## 6. Evitare la pausa del progetto gratuito

Supabase mette in pausa i progetti gratuiti dopo **7 giorni** senza attività. Il file `.github/workflows/tieni-sveglio.yml` interroga il database ogni 3 giorni, e per funzionare ha bisogno di due "secrets" nel repository GitHub:

1. Sul repository GitHub: **Settings → Secrets and variables → Actions → New repository secret**.
2. Crea `SUPABASE_URL` con il Project URL.
3. Crea `SUPABASE_KEY` con la chiave pubblica.

Se il progetto finisse comunque in pausa, si riattiva dalla dashboard di Supabase con **Restore project**.

## Limiti del piano gratuito (ottobre 2026)

- 2 progetti attivi, 500 MB di database
- 200 connessioni in tempo reale contemporanee, 2 milioni di messaggi al mese
- accessi anonimi: 30 all'ora per indirizzo IP (modificabile in *Authentication → Rate Limits*)

Per serate tra amici sono limiti lontanissimi.

## Facoltativo, più avanti

Supabase consiglia un CAPTCHA invisibile (es. Cloudflare Turnstile) quando l'accesso anonimo è aperto a tutti, per evitare che qualcuno crei utenti finti in massa. Per un sito condiviso tra amici non è indispensabile: si può aggiungere se il link diventa pubblico.
