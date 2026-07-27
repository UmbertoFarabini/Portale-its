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

### S3 - Gli accessi pubblici al bucket non sono bloccati 
- **Gravita**: SERIO
- **Fatto**: Nessuna cifratura a riposo: ne sul bucket ne sulla tabella delle iscrizioni, che contiene dati di persone
- **Conseguenza**: I dati sensibili (bucket S3 e tabella iscrizioni) restano leggibili in chiaro
- **Rimedio**: 	cifratura sul bucket, chiave gestita da noi sulla tabella
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

##OPERABILITA

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
