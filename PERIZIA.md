# PERIZIA - Portale corsi ITS
Squadra: ___   Binario: CFN / Terraform
Data: 27/07/2026

## Sicurezza

### S1 - Il bucket del sito e aperto a chiunque
- **Gravita**: BLOCCANTE
- **Fatto**: la bucket policy consente s3:*
  a Principal "*".
- **Conseguenza**: chiunque su internet puo
  modificare o cancellare le pagine del portale.
- **Rimedio**: rimuovere la policy pubblica,
  riattivare il blocco accessi pubblici,
  pubblicare solo dalla pipeline.

### S2 - Gli accessi pubblici al bucket non sono bloccati 
- **Gravita**: BLOCCANTE
- **Fatto**: Il blocco degli accessi pubblici e assente     (CloudFormation) o esplicitamente disattivato su tutte e quattro le voci (Terraform).
- **Conseguenza**: chiunque su internet puo
   avere accesso ai file nel bucket.
- **Rimedio**: Bloccare l'accesso pubblico ai bucket sul file   main.tf

### S3 - Gli accessi pubblici al bucket non sono bloccati 
- **Gravita**: BLOCCANTE
- **Fatto**: Credenziali in chiaro in tre posti: password FTP in config/impostazioni.txt, token nel Default del parametro CloudFormation, e nel default della variabile Terraform
- **Conseguenza**: Le password sono visibili a tutti
- **Rimedio**: rimuovere, considerarle compromesse e cambiarle, montare un controllo sui segreti

### S4 - Nessuna cifratura a riposo
- **Gravita**: SERIO
- **Fatto**: Nessuna cifratura a riposo: ne sul bucket ne sulla tabella delle iscrizioni, che contiene dati di persone
- **Conseguenza**: I dati sensibili (bucket S3 e tabella iscrizioni) restano leggibili in chiaro
- **Rimedio**: 	cifratura sul bucket, chiave gestita da noi sulla tabella

## Affidabilità

## A1 - Il bucket non ha il versioning
- **Gravità**: BLOCCANTE
- **Fatto**: il bucket non ha il versioning attivato e la pagina pubblicata non ha una versione precedente conservata.
- **Conseguenza**: in caso di errore o modifica non voluta, non è possibile ripristinare una versione precedente del sito.
- **Rimedio**: attivare il versioning sul bucket e conservare gli artefatti generati dalla pipeline per il rollback.

## A2 - La tabella delle iscrizioni non ha backup
- **Gravità**: SERIO
- **Fatto**: la tabella delle iscrizioni non ha il ripristino puntuale attivato.
- **Conseguenza**: in caso di cancellazione, corruzione o errore operativo, i dati delle iscrizioni non possono essere recuperati in modo affidabile.
- **Rimedio**: attivare il ripristino puntuale della tabella DynamoDB.

## A3 - Non esiste una procedura di ritorno affidabile
- **Gravità**: BLOCCANTE
- **Fatto**: il runbook indica di «rifare il punto 5 con la versione vecchia, se qualcuno ce l’ha», senza un meccanismo definito per ripubblicare una versione precedente.
- **Conseguenza**: in caso di problema, il ritorno a una release precedente non è operativo e dipende da un passaggio manuale non formalizzato.
- **Rimedio**: definire una procedura di rollback automatizzata, con versione precedente conservata e ripubblicazione guidata dalla pipeline.

## Operabilità

## O1 - La pubblicazione è manuale e dipende da una persona e da un portatile
- **Gravità**: BLOCCANTE
- **Fatto**: la pubblicazione avviene in modo manuale, in nove passaggi, e dipende da una persona e da un portatile specifico.
- **Conseguenza**: il processo è fragile, poco ripetibile e soggetto a errore umano o perdita di accesso al dispositivo utilizzato.
- **Rimedio**: introdurre una pipeline automatica di pubblicazione con approvazione e controlli di sicurezza.

## O2 - Non esistono log di accesso al bucket
- **Gravità**: SERIO
- **Fatto**: non è possibile sapere chi ha caricato cosa nel bucket, e il cliente richiede un audit chiaro delle operazioni.
- **Conseguenza**: manca la tracciabilità delle modifiche, rendendo difficile verificare chi ha eseguito una pubblicazione o una modifica.
- **Rimedio**: abilitare i log di accesso del bucket e mantenere una cronologia delle pubblicazioni.

## O3 - La verifica dopo la pubblicazione è manuale
- **Gravità**: SERIO
- **Fatto**: la verifica post-pubblicazione è effettuata «a occhio», senza un controllo automatico.
- **Conseguenza**: errori di pubblicazione o problemi di contenuto possono passare inosservati fino a quando non vengono rilevati manualmente.
- **Rimedio**: introdurre un controllo automatico dopo il deploy, con validazione della disponibilità e della correttezza del sito.


## Evolvibilità

## E1 - Non esistono test automatizzati
- **Gravità**: BLOCCANTE
- **Fatto**: il documento contiene un errore di calcolo nelle ore totali, con un valore riportato di 260 ore mentre la somma della colonna restituisce 320 ore, e non esistono test che ne garantiscano la correttezza.
- **Conseguenza**: il contenuto è fragile, soggetto a errori di calcolo e non verificabile automaticamente.
- **Rimedio**: correggere il calcolo e introdurre test automatizzati che verificano il totale e la coerenza dei dati.

## E2 - Non esiste un lockfile delle dipendenze
- **Gravità**: SERIO
- **Fatto**: le dipendenze non sono bloccate da un lockfile, quindi due build o installazioni successive possono risolvere versioni diverse.
- **Conseguenza**: il comportamento della build può cambiare nel tempo, rendendo meno ripetibile e più difficile da auditare.
- **Rimedio**: generare e committare il lockfile, ad esempio con npm ci e il file package-lock.json.

## E3 - Un solo ambiente e nessun parametro per l'ambiente
- **Gravità**: SERIO
- **Fatto**: i nomi delle risorse sono scritti a mano e non esiste un parametro per l'ambiente, quindi non è possibile avere un ambiente di test o di staging separato.
- **Conseguenza**: non esiste un luogo in cui provare le modifiche prima di distribuire in produzione, aumentando il rischio di errori.
- **Rimedio**: introdurre un parametro Env e derivare i nomi delle risorse da esso, creando un ambiente dedicato per le prove.

## Costi

## C1 - Dipendenza non utilizzata e potenziale superficie di attacco
- **Gravità**: SERIO
- **Fatto**: è presente una dipendenza dichiarata ma mai usata, come left-pad, che aumenta la superficie di attacco del progetto.
- **Conseguenza**: dipendenze inutilizzate espongono il progetto a rischi inutili e complicano la manutenzione.
- **Rimedio**: rimuovere la dipendenza non utilizzata e mantenere solo le librerie realmente necessarie.

## C2 - Nessuna risorsa ha tag di governance
- **Gravità**: SERIO
- **Fatto**: nessuna risorsa del portale è taggata con Owner, Env o Progetto, rendendo impossibile distinguere responsabilità e ambiente di appartenenza.
- **Conseguenza**: non è possibile capire chi è responsabile delle risorse, né stimare in modo affidabile il costo del portale o l'ambito di impatto delle modifiche.
- **Rimedio**: aggiungere i tag Owner, Env e Progetto a tutte le risorse e introdurre un controllo che li richieda in modo automatico.

## C3 - Parametro del token gestionale non usato
- **Gravità**: SERIO
- **Fatto**: il parametro dedicato al token del gestionale non viene usato da nessuna risorsa, ma resta comunque presente come segreto esposto.
- **Conseguenza**: il progetto mantiene un segreto inutilizzato, aumentando il rischio di configurazione obsoleta e di gestione errata dei segreti.
- **Rimedio**: eliminare il parametro non usato e conservare i segreti solo dove effettivamente servono.

