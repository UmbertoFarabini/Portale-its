# Riscontro alle richieste del 27/07

## CR-1 - Due nuove unita formative
ACCETTATA. Online, versione `dbc4689` (run #2 della pipeline Release,
completato con successo in 1m23s: build, collaudo su ambiente di test,
approvazione, pubblicazione).

Il totale ore in pagina e passato da 320 a 395, aggiornato automaticamente
dal generatore - nessun calcolo manuale, nessun rischio di sbagliare la somma.

URL pubblica: https://wabonnie.github.io/Portale-its/

Da adesso questa modifica la puo fare la segreteria da sola, in pochi minuti,
senza bisogno di un tecnico: si modifica un file, si apre una richiesta di
verifica, e il sistema controlla da solo che tutto sia corretto prima di
pubblicare.

## CR-2 - Caricamento file dal fornitore
ACCETTATA CON CONDIZIONE. Non diamo accesso all'archivio del sito - quello
resta raggiungibile solo dalla pipeline, come richiesto dal cliente stesso.

Abbiamo creato un bucket separato dedicato allo scambio
(`portale-its-scambio-*`), cifrato, versionato e con i log di accesso
attivi come tutte le altre risorse. Il fornitore riceve un'identita propria
che puo *solo scrivere*, e *solo* dentro la sua cartella
(`fornitore-iscrizioni/`) - non puo leggere, cancellare, ne toccare
nient'altro, ne dentro quel bucket ne altrove.

Le credenziali di accesso non vengono generate nel codice
dell'infrastruttura (lo stesso errore che ha causato il finding S3):
verranno create separatamente e consegnate al fornitore in modo sicuro,
fuori da questo canale.

Tempo di implementazione: gia incluso nel lavoro odierno.

## CR-3 - Rimettere i permessi pubblici
(in lavorazione)
