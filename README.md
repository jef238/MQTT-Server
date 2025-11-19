# MQTT Server

## Cos'è
E' un dispositivo che permette il controllo remoto degli input/output di un microcontroller (in questo caso l'Attiny2313) tramite il protocollo MQTT.
Per saperne di più sul protocollo MQTT:

https://mqtt.org/ 

## Principio di funzionamento
Questo progetto è basato sulla scheda JefBoard che ospita a bordo un microcontroller Attiny2313 e il modulo WIFI ESP01 (https://github.com/jef238/jefBoard). Il modulo ESP01 consentirà la comunicazione verso il broker MQTT e verso l'Attiny2313, si occuperà inoltre di fornire un interfaccia per la configurazione della connessione WIFI. La comunicazione SERIALE tra l'ESP01 e l'Attiny2313 consentirà la ricezione o l'invio dei comandi verso gli specifici TOPICS MQTT.

![diagramma](diagramma.png)

Il protocollo MQTT permette di utilizzare topics differenti per gestire le comunicazioni con il microcontroller, in particolare:

- jbrd_idunivoco/status -> Viene utilizzato per comunicare informazioni circa lo stato di funzionamento del sistema
- jbrd_idunivoco/IN -> Tutti i comandi da inviare verso il microcontroller
- jbrd_idunivoco/OUT ->Tutti gli output in uscita dal microcontroller

## Formato dei comandi
Nel protocollo MQTT il contenuto del messaggio scambiato tra il client ed il broker è chiamto patyload e può contenere 
qualsiasi tipo di dato binario come testo, XML, JSON immagini ecc.ec.. In questo caso i messaggi saranno costituiti dal pattern
*ABCD#
## Componenti

## Codice ESP01 (LUA scripts)

## Codice JefBOARD (Attiny2313)

## Esempio reale di funzionamento

