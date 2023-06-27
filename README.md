# Data-Pipeline
## Scenario
Nel territorio siciliano sono state installate 185 stazioni meteo che generano dati sulle precipitazioni (in millimetri) e sulla temperatura (in gradi centigradi). Il numero di stazioni può variare in base all’attivazione di nuove stazioni o alla dismissione delle esistenti. Ogni stazione è identificata in modo univoco dal nome della località in cui è stata installata. Alla fine di ogni giornata, ciascuna stazione genera due file in formato CSV, uno per la temperatura e uno per le precipitazioni. Ogni file contiene tutte le misurazioni del parametro corrispondente (ad esempio temperatura o precipitazioni) effettuate durante la giornata, con una periodicità minima di 5 minuti (che potrebbe variare in intervalli di 5, 10, 15, 20, 30 minuti, ecc.). La tipologia del file (parametro misurato) e il giorno di riferimento possono essere identificati sia dal nome del file stesso che all'interno del suo contenuto.
## Specifiche
La pipeline da progettare e realizzare deve  raccogliere i file, sia sulle precipitazioni che sulle temperature, estrarre i dati ed associarli a ciascuna specifica stazione meteo, in separate tabelle per pluvio, piuttosto che per temperatura.
I file ricevuti per essere considerati validi dovranno superare i seguenti controlli:
1. Correttezza formato nome file
2.  Correttezza formato file (prime tre righe di intestazione, sono corrette? Il formato dei dati su ciascuna colonna è corretto? Es. prima colonna contiene un 	timestamp, seconda colonna contiene valori
3. Corrispondenza dato nel file con dato nel nome del file (file pluvio contiene dati pluviometrici? Controllare sia intestazione riga che range valori presenti)
Durante la verifica della validità dei file bisogna tenere in considerazione che i valori negativi sono codici di errore, che in un primo passaggio di verifica di formattazione del file possono essere accettati come validi

Dopo aver superato i controlli, i file validati e non devono essere archiviati inalterati e caricati su un Database NoSql MongoDb. Una volta disponibili i dati Raw Validati , si effettuano due passaggi di correzione ed arricchimento:
1. Correzione:
	- Si identificano i dati di errore (valore negativo) e quelli fuori scala e per ciascuno di essi si applica un algoritmo di correzione che sostituisce il valore mancante con l’ultimo valore valido ricevuto
2. Arricchimento:
	- Sulla base di una mera tabella di lookup, si associa ogni stazione meteo ad una zona, intesa come rappresentanza di un gruppo di stazioni.
	Successivamente si salvano tali dati su un database MySQL

In seguito al salvataggio dei dati corretti e arricchiti segue un ultimo stadio di processing, che consiste nel generare dati aggregati come segue:
 
- Temperatura ( Calcolare la temperatura media per stazione meteo)
  	- oraria (per ora piena, es. tra le 13:00 e le 13:55)
	- giornaliera (es. tra la 00:00 e le 23:55)
   	- giornaliera dalle 9 (tra le 9:00 e le 8:55)
  	- Calcolare la temperatura media di ciascun gruppo
- Precipitazioni ( calcolare i valori cumulati (sommando) per stazione meteo )
  	- orario
  	- giornaliero
 	- giornaliero alle 9
  	- Calcolare il valore di precipitazione cumulata per ciascun gruppo
  	- 
Una volta terminata l’esecuzione della pipeline i dati aggregati generati dovranno essere visualizzati attraverso il software Tableau  ed inoltre dovrà essere inviata una mail utilizzando il server SMTP di Gmail, ad un indirizzo prestabilito per informare l’utente della disponibilità dei dati aggiornati.


