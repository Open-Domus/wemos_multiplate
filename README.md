# Wemos Multiplate
(placeholder name)

Multifunctional device with esphome compatible sensors and interactive tools to improve your smart home experience.

![front](images/2-2-front.png)

## Features

- Compatible with the all-popular development boards
  - Wemos D1 mini (ESP8266 based, micro-usb connector)
  - Wemos S2 mini (ESP32 based, usb-c connector)
- 128x64 OLED display
- LD2410C 24Ghz human presence sensor
- 2 addressable RGB LEDs
- Beeper
- SHT31 temperature/humidity sensor
- IR emitter
- BH1750 ambient light sensor (optional)
- APDS9960 gesture sensor (optional)
- Pin headers for external 5v relay module (optional)
- Extra pin headers for further expandability
- You can fit in a 503 wall receptacle, or just keep it as a small box on your desk
- 3D printable enclosures

## Esphome compiling and flashing
- install esphome on your machine. https://esphome.io/guides/installing_esphome.html
- duplicate the `secrets_sample.yaml` file, rename it to `secrets.yaml`, and fill your wifi SSID data in.
- if you want to use a Wemos D1 mini:
  - duplicate `esphome/multiplate_d1mini.yml` file, rename it to your liking.
- if you want to use a Wemos S2 mini:
  - duplicate `esphome/multiplate_s2mini.yml` file, rename it to your liking.
- open the duplicated file and edit `device_name` to your liking, and comment/uncomment the modules you want to use.
- connect your Wemos board via USB and flash it using the `esphome run` command.
- disconnect the Wemos board, insert it into the multiplate, and enjoy.

## Home Assistant device page preview

![Screenshot 2024-05-21 at 15 51 57](https://github.com/Open-Domus/wemos_multiplate/assets/6085909/b701bf96-6485-4ce5-b446-c2142eaa4968)

## Customizing modules

### mmWave presence sensor

The sensor internally has 8 'gates', each one representing a distance area from the sensor. The sensitivity of each gate can be tuned using an android app.
- Download https://play.google.com/store/apps/details?id=com.hlk.hlkradartool&hl=en&gl=US&pli=1
- Enable bluetooth on the sensor (you can do this from the Home Assistant device page)
- Open the app and connect to the sensor
- Enable Engineering Mode and tune the gates. The lower the value, the higher the accuracy (less energy required to trigger the gate)
- Refer to this page for more info. https://wiki.apolloautomation.com/books/msr-1/page/how-to-tune-mmwave-using-home-assistant

Note: it is possible to expose all the sensitivity tuning controls via ESPhome to Home Asisstant, but we are not doing that. This reduces computational load on the Wemos D1 mini.

### IR remote

If you want to use the IR leds as a remote, please add any entity you want to use the IR transmitter with, to the main yml file. Use `ir_remote` as the transmitter id. Example:

```
climate:
  - platform: daikin
    name: ${device_name}-climate
    transmitter_id: ir_remote
    receiver_id: ir_rx
```
