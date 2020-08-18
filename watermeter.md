# WML Watermeter
Each year my watermeter needs to be manually checked. The current reading needs to be reported to the provider in order to send me the yearly bill. This process is time consuming and exhausting to do. About time to fix this and make the dumb meter smart. Of course I could have installed a flow meter, but what's the fun of that..

## BOL
- 3d printed water meter holder [Download](https://www.thingiverse.com/thing:4146391) (credits to [Jover68 on thingiverse](https://www.thingiverse.com/Jover68))
- Wemos D1 mini with mini usb cable
- Esphome (installed or docker)
- Proximity sensor (low voltage one, or you will need extra power adapter) [Buy here](https://nl.aliexpress.com/item/32826250006.html?spm=a2g0s.9042311.0.0.27424c4dB8gKjc)

## ESPHome
After soldering the wires you will need to flash the Wemos with the correct esp config:

```markdown
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
![Image](https://github.com/CerielRoland/Domotica/blob/master/Watermeter/images/20200818_204403.jpg)
