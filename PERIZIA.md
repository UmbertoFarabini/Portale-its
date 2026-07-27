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