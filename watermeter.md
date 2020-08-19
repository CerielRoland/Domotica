# WML Watermeter Introduction
Each year my watermeter needs to be manually checked. The current reading needs to be reported to the provider in order to send me the yearly bill. This process is time consuming and exhausting to do. About time to fix this and make the dumb meter smart. Of course I could have installed a flow meter, but what's the fun of that..

My watermeter is a dumb meter, but has a metal needle that spins if water is used. Every complete spin is exactly 0.001 m3 water and can be used as a reporting unit for my smart domotica system. Using a proximity sensor that gives a pulse everytime the metal passes I can report the usage to my dashboard. 
So in order:
1. Sensor gives a pulse to a Wemos D1 mini with ESPHome.
2. ESPHome reports the pulse to Home Assistant.
3. NodeRed picks up the pulses and stores them in a counter variable.
4. NodeRed checks the current meter report, adds the counter variable and stores it again in:
4.1. Home Assistant value
4.2. InfluxDB measurement
5. NodeRed restart the loop.

# Table of content 
* auto-gen TOC:
{:toc}

# BOL
- 3D printed water meter holder [Download](https://www.thingiverse.com/thing:4146391) (credits to [Jover68 on thingiverse](https://www.thingiverse.com/Jover68))
- Wemos D1 mini with mini usb cable
- Esphome (installed or docker)
- Proximity sensor (low voltage one, or you will need extra power adapter) [Buy here](https://nl.aliexpress.com/item/32826250006.html?spm=a2g0s.9042311.0.0.27424c4dB8gKjc)

# Showcase
## 3d model of holder
<img src="https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/3d_WML.png?raw=true" width="50%" height="50%">
## Proximity meter
<img src="https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/20200818_204403.jpg?raw=true" width="50%" height="50%"><img src="https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/20200818_204409.jpg?raw=true" width="50%" height="50%">
## Wemos D1 mini
<img src="https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/20200818_204450.jpg?raw=true" width="50%" height="50%"><img src="https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/20200818_204454.jpg?raw=true" width="50%" height="50%">
## NodeRed
<img src="https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/NodeRed_WML.png?raw=true">

# Buidling instructions

## Wemos D1 mini
First step is to solder the proximity sensor to the Wemos D1 mini.
- GND > GND
- DC > 5V
- Pulse > D0

## ESPHome
After soldering the wires you will need to flash the Wemos with the correct esp config.
_Please note that the pinout can be different on other boards!_

```yaml
esphome:
  name: wml_watermeter
  platform: ESP8266
  board: d1_mini

wifi:
  ssid: "xxx"
  password: "xxx"
  manual_ip:
    static_ip: xxx
    gateway: xxx
    subnet: xxx
  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Fallback Hotspot"
    password: "xxx"

captive_portal:

# Enable logging
logger:

# Enable Home Assistant API
api:
  password: "xxx"

ota:
  password: "xxx"

binary_sensor:
  - platform: gpio
    pin: 16
    device_class: opening
    name: "Pulse Counter"
```

## Print the holder & assemble the unit
Print the holder for you meter or order it at your favorite 3d prinshop.
Assemble the unit and fix it to your watermeter.
Connect the unit to your power source.

## Check the unit for operation
Open the logfile at your esphome deployment to check if it works.

```markdown
INFO Reading configuration /config/xxx.yaml...
INFO Starting log output from xxx using esphome API
INFO Connecting to xxx:6053 (xxx)
INFO Successfully connected to xxx
[12:22:12][I][app:100]: ESPHome version 1.14.3 compiled on May 26 2020, 10:45:13
[12:22:12][C][gpio.binary_sensor:015]: GPIO Binary Sensor 'Pulse Counter'
[12:22:12][C][gpio.binary_sensor:015]:   Device Class: 'opening'
[12:22:12][C][gpio.binary_sensor:016]:   Pin: GPIO16 (Mode: INPUT)
[12:22:12][C][logger:175]: Logger:
[12:22:12][C][logger:176]:   Level: DEBUG
[12:22:12][C][logger:177]:   Log Baud Rate: 115200
[12:22:12][C][logger:178]:   Hardware UART: UART0
[12:22:43][D][binary_sensor:036]: 'Pulse Counter': Sending state OFF
[12:22:46][D][binary_sensor:036]: 'Pulse Counter': Sending state ON
```
Your binary sensor should report ON everytime the sensor reads the pin on the meter.
Be sure to check if the unit works if you don't touch it, my meter was very picky on the contact.
This could require some cursing and yelling....

## Add the esphome to your Home Assistant
Adding the esphome node to your Home Assistant can be done via the integration in the system menu.
Follow the guides if you need help:
- [Basic ESPHOME](https://esphome.io/guides/getting_started_hassio.html)
- [ESPHome integration](https://www.home-assistant.io/integrations/esphome/)

## NodeRed flow
Next step is to set up the Node-Red flow, use my version as a starter or create your own:

```yaml
[{"id":"1cdcfa04.712ba6","type":"group","z":"570b20db.267b5","name":"Smart Home - WML","style":{"stroke":"#0070c0","fill":"#bfdbef","label":true,"color":"#000000"},"nodes":["11527669.5905aa","1bfe2640.a5e5ca","3c995c09.1a8694","bbe64b97.0cad88","6dc21f10.ac4d1","e6a6fe9e.b98db","31a9cbe1.bf6224","ed97ff5f.ad098","f1221544.dbd628","da000815.9d8e58","a7ff92e.e973e7","5e2b4866.660b98"],"x":34,"y":179,"w":1052,"h":282},{"id":"11527669.5905aa","type":"ha-entity","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"WML Waterstand","server":"6d7efc20.573f74","version":1,"debugenabled":false,"outputs":1,"entityType":"sensor","config":[{"property":"name","value":"wml_waterstand"},{"property":"device_class","value":""},{"property":"icon","value":"mdi:cup-water"},{"property":"unit_of_measurement","value":"m3"}],"state":"waterstand","stateType":"msg","attributes":[],"resend":true,"outputLocation":"","outputLocationType":"none","inputOverride":"allow","x":970,"y":420,"wires":[[]]},{"id":"1bfe2640.a5e5ca","type":"inject","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"Manual set wml stand","props":[{"p":"payload"}],"repeat":"","crontab":"","once":false,"onceDelay":0.1,"topic":"","payload":"","payloadType":"date","x":300,"y":420,"wires":[["3c995c09.1a8694"]]},{"id":"3c995c09.1a8694","type":"function","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"Set WML Stand","func":"// Manually set the WML waterstand\nmsg.waterstand = 740.837\nmsg.waterstand = (parseFloat(msg.waterstand).toFixed(3))\n\n// Return msg\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":540,"y":420,"wires":[["11527669.5905aa"]]},{"id":"bbe64b97.0cad88","type":"function","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"+1 increment","func":"var pulse_counter = global.get('wml_pulse_counter');\nif( isNaN(pulse_counter) ) {\n    global.set('wml_pulse_counter', 1);\n}\nelse {\n    global.set('wml_pulse_counter', (global.get('wml_pulse_counter')+1));\n}\nmsg.topic = \"auto pulse\";\n\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":530,"y":220,"wires":[["31a9cbe1.bf6224"]]},{"id":"6dc21f10.ac4d1","type":"inject","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"Manual increment wml counter","props":[{"p":"payload"}],"repeat":"","crontab":"","once":false,"onceDelay":0.1,"topic":"","payload":"","payloadType":"date","x":270,"y":260,"wires":[["bbe64b97.0cad88"]]},{"id":"e6a6fe9e.b98db","type":"function","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"Increment + reset","func":"// increment new pulse_count\ncurrent_wml_stand = parseFloat(msg.payload[0].state);\ncurrent_counter = parseFloat((global.get('wml_pulse_counter')*0.001))\nnew_wml_stand = current_wml_stand + current_counter;\nnew_formated_wml_stand = (parseFloat(new_wml_stand).toFixed(3))\n\n// reset pulse counter\nglobal.set('wml_pulse_counter', 0);\n\nmsg.waterstand = new_formated_wml_stand\nmsg.payload = {\n    \"entity_id\" : \"wml.waterstand\",\n    \"value\": new_formated_wml_stand\n};\n\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":750,"y":340,"wires":[["31a9cbe1.bf6224","11527669.5905aa"]]},{"id":"31a9cbe1.bf6224","type":"debug","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"DEBUG","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","targetType":"full","statusVal":"","statusType":"auto","x":960,"y":220,"wires":[]},{"id":"ed97ff5f.ad098","type":"inject","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"Manual update wml counter","props":[{"p":"payload"}],"repeat":"","crontab":"","once":false,"onceDelay":0.1,"topic":"","payload":"","payloadType":"date","x":280,"y":320,"wires":[["f1221544.dbd628"]]},{"id":"f1221544.dbd628","type":"ha-get-entities","z":"570b20db.267b5","g":"1cdcfa04.712ba6","server":"6d7efc20.573f74","name":"Get WML Stand","rules":[{"property":"entity_id","logic":"is","value":"sensor.wml_waterstand","valueType":"str"}],"output_type":"array","output_empty_results":false,"output_location_type":"msg","output_location":"payload","output_results_count":1,"x":540,"y":340,"wires":[["e6a6fe9e.b98db"]]},{"id":"da000815.9d8e58","type":"server-state-changed","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"","server":"6d7efc20.573f74","version":1,"exposeToHomeAssistant":false,"haConfig":[{"property":"name","value":""},{"property":"icon","value":""}],"entityidfilter":"binary_sensor.pulse_counter","entityidfiltertype":"exact","outputinitially":false,"state_type":"str","haltifstate":"on","halt_if_type":"str","halt_if_compare":"is","outputs":2,"output_only_on_state_change":true,"x":230,"y":220,"wires":[["bbe64b97.0cad88"],[]]},{"id":"a7ff92e.e973e7","type":"inject","z":"570b20db.267b5","g":"1cdcfa04.712ba6","name":"Interval 30s","repeat":"30","crontab":"","once":false,"onceDelay":0.1,"topic":"","payload":"","payloadType":"date","x":330,"y":360,"wires":[["f1221544.dbd628"]]},{"id":"5e2b4866.660b98","type":"influxdb out","z":"570b20db.267b5","g":"1cdcfa04.712ba6","influxdb":"90a231e6.0de5b","name":"","measurement":"m³","precision":"","retentionPolicy":"","x":950,"y":380,"wires":[]},{"id":"6d7efc20.573f74","type":"server","z":"","name":"Home Assistant","legacy":false,"rejectUnauthorizedCerts":true,"ha_boolean":"y|yes|true|on|home|open","connectionDelay":true,"cacheJson":true},{"id":"90a231e6.0de5b","type":"influxdb","z":"","hostname":"172.20.3.28","port":"8086","protocol":"http","database":"hass_db","name":"Influx DB","usetls":false,"tls":""}]
```

## Dashboard
If all goes well, you will now have a dashboard with your water usage:
<img src="https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/WML_HASS.png?raw=true" width="50%" height="50%">
