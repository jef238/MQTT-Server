# MQTT Server

## Cos'è
E' un dispositivo che permette il controllo remoto degli input/output di un microcontroller (in questo caso l'Attiny2313) tramite il protocollo MQTT.
Per saperne di più sul protocollo MQTT:

https://mqtt.org/ 

## Principio di funzionamento
Questo progetto è basato sulla scheda JefBoard che ospita a bordo un microcontroller Attiny2313 e il modulo WIFI ESP01 (https://github.com/jef238/jefBoard). Il modulo ESP01 consentirà la comunicazione verso il broker MQTT e verso l'Attiny2313, si occuperà inoltre di fornire un interfaccia per la configurazione della connessione WIFI. La comunicazione SERIALE integrata in JefBoard tra l'ESP01 e l'Attiny2313 consentirà la ricezione o l'invio dei comandi verso gli specifici TOPICS MQTT.

![diagramma](diagramma.png)

Il protocollo MQTT permette di utilizzare topics differenti per gestire le comunicazioni con il microcontroller, in particolare:

- jbrd_idunivoco/status -> Viene utilizzato per comunicare informazioni circa lo stato di funzionamento del sistema
- jbrd_idunivoco/IN -> Tutti i comandi da inviare verso il microcontroller (JefBoard)
- jbrd_idunivoco/OUT ->Tutti gli output in uscita dal microcontroller (JefBoard)

## Formato dei comandi
Nel protocollo MQTT il contenuto del messaggio scambiato tra il client ed il broker è chiamato payload e può contenere 
qualsiasi tipo di dato binario come testo, XML, JSON immagini ecc.ec.. In questo caso utilizzeremo dei messaggi costituiti da stringhe che iniziano con il carattere * e terminano con il carattere #.

Esempio: *PD51#

La lunghezza complessiva di tale comando comprensivo dei caratteri * e # sarà di 8 caratteri (ma è modificabile)

## Codice ESP01 (LUA scripts)
Il firmware da caricare su ESP01 è nodeMCU compilato con i moduli opzionali ENDUSR e MQTT che forniscono rispettivamente il modulo per l'autoconfigurazione del WIFI e il supporto al protocollo MQTT (nodemcu-release-ENDUSR_MQTT-integer.bin); Per informazioni su come eseguire il flash del firmware vedi https://github.com/jef238/jefBoard.
Una volta caricato il firmware nodeMCU è possibile caricare gli script LUA. Il file denominato init.lua viene caricato all'avvio in maniera automatica. E' possibile utilizzare il software ESPlorer (https://github.com/4refr0nt/ESPlorer) sia per caricare sia gli script LUA che per gestire al meglio l'ESP01.
ATTENZIONE: Il codice contenuto nel file init.lua attiva una funzione di callback sulla seriale per consentire lo scambio di informazioni bidirezionale verso l'Attiny2313 dopo 10 secondi dall'avvio. Dopo non sarà più possibile utilizzare tool di gestione dell'ESP01 come ESPlorer che utilizzano appunto la seriale. In questo range di 10 secondi è possibile sostituire e/o modificare l'init.lua per ritornare ad utilizzare ESPlorer.
Per eseguire i test utilizzeremo un broker MQTT demo di test broker.hivemq.com.
I file da caricare su ESP01 saranno due, init.lua e mqtt.lua:



## Codice JefBOARD (Attiny2313)

## Esempio reale di funzionamento

