# MQTT Server

## Cos'è
E' un dispositivo che permette il controllo remoto degli input/output di un microcontroller (in questo caso l'Attiny2313) tramite il protocollo MQTT.
Per saperne di più sul protocollo MQTT:

https://mqtt.org/ 

## Principio di funzionamento
Questo progetto è basato sulla scheda JefBoard che ospita a bordo un microcontroller Attiny2313 e il modulo WIFI ESP01 (https://github.com/jef238/jefBoard). Il modulo ESP01 consentirà la comunicazione verso il broker MQTT e verso l'Attiny2313, si occuperà inoltre di fornire un interfaccia per la configurazione della connessione WIFI. La comunicazione SERIALE tra l'ESP01 e l'Attiny2313 consentirà la ricezione o l'invio dei comandi verso gli specifici TOPICS MQTT.

Immagine

Il protocollo MQTT permette di utilizzare topics differenti per gestire le comunicazioni con il microcontroller, in particolare:



## Componenti

## Codice ESP01 (LUA scripts)

## Codice JefBOARD (Attiny2313)

## Utilizzi

