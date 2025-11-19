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

Inoltre nel file init.lua è stato inserita la possibilità di resettare le impostazioni WIFI precedentemente salvate portando a massa il pin GPI00.

I file da caricare su ESP01 saranno due, init.lua e mqtt.lua:

### init.lua

```
-- jefBoard ESP01 vers. 1.0

uart.setup(0, 9600, 8, uart.PARITY_NONE, uart.STOPBITS_1, 1)

print("\n\nJefBoard is loading...")
print("\n10 seconds to activate ESP01 serial input callback to MQQT")

wifi.sta.autoconnect(1)

uid = string.sub(wifi.sta.getmac(),-5)
uid = string.gsub(uid,":","")

function main()

dofile("mqtt.lua")

end

enduser_setup.start("jefBoard_" .. uid, main)

function clearC()
wifi.sta.clearconfig()
end

gpio.mode(3, gpio.INT)
gpio.trig(3, "down", clearC)

--Attendi 10 secondi prima di attivare callback dell'input seriale dell'esp01 to-> mqqt
--In questo range è ancora possibile utilizzare ESPlorer e disabilitare il callback decommentando
--uart.on("data")

tmr.create():alarm(10000, tmr.ALARM_SINGLE, function()

uart.on("data", "#",
  function(data)
   m:publish("jbrd_" .. uid .. "/OUT", data, 0, 0)
  end, 
  0)
  
end)

--uart.on("data")
```
### mqtt.lua

```
-- init mqtt client with logins, keepalive timer 120sec

m = mqtt.Client("jbrd" .. uid, 120)

-- setup Last Will and Testament (optional)
-- Broker will publish a message with qos = 0, retain = 0, data = "offline"
-- to topic "/lwt" if client don't send keepalive packet
m:lwt("/lwt", "offline", 0, 0)

m:on("offline", function(client) print ("offline") end)

-- on publish message receive event FROM jefboard attiny2313 
m:on("message", function(client, topic, data)  
  if data ~= nil then
    --send topic subscribed data to serial -> attiny2313
    print(data)   
  end
end)

-- on publish overflow receive event
m:on("overflow", function(client, topic, data)
  print(topic .. " partial overflowed message: " .. data )
end)


function handle_mqtt_error(client, reason)
  tmr.create():alarm(10 * 1000, tmr.ALARM_SINGLE, do_mqtt_connect)
end

function do_mqtt_connect()
  m:connect("broker.hivemq.com", 1883, false, function(client)  
    print("connected")
    m:publish("jbrd_" .. uid .. "/status", "jefBoard is alive! : " .. tmr.now(), 0, 0)    
    m:subscribe("jbrd_" .. uid .. "/IN", 0, function(client) print("subscribe success") end)
    end,
  handle_mqtt_error 
  )
end

do_mqtt_connect()
```

## Codice JefBOARD (Attiny2313)

## Esempio reale di funzionamento

