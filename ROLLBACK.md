# Prova di rollback

## Incidente
Titolo del corso ACA sostituito per errore.
Commit colpevole: `9121986`

## Ripristino
- Metodo: `git revert` + pipeline con approvazione
- Guasto visibile online:   10:23
- Versione buona online:    10:39
- MTTR: 16 minuti

## Cosa ha rallentato
14 minuti su 16 sono stati attesa dell'approvazione.
La pipeline ha impiegato 2 minuto e 10.

## Cosa proporrei al cliente
Percorso d'emergenza con revisori multipli, cosi'
l'approvazione non dipende da una sola persona.