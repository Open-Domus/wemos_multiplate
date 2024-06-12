# Wemos Multiplate
(placeholder name)

Multifunctional device with esphome compatible sensors and interactive tools to improve your smart home experience.

![front](images/2-2-front.png)

## Features

- OLED display (128x32 or 128x64 are supported)
- LD2410C 24Ghz presence sensor
- 2 addressable RGB LEDs
- beeper
- SHT31 temperature/humidity sensor
- 3 IR emitter LEDs
- BH1750 ambient light sensor (optional)
- APDS9960 gesture sensor (optional)
- pin headers for external 5v relay module (optional)
- extra pin headers for further expandability
- fits in a 503 wall receptacle
- 3D printable cover

## Esphome compiling and flashing
- install esphome on your machine. https://esphome.io/guides/installing_esphome.html
- duplicate the `secrets_sample.yaml` file, rename it to `secrets.yaml`, and fill your wifi SSID data in.
- duplicate `esphome/multiplate.yml` file, rename it to your liking.
- open the duplicated file and edit `device_name` to your liking, and comment/uncomment the modules you want to use.
- connect the Wemos D1 mini via USB and flash it using the `esphome run` command.
- disconnect the Wemos D1 mini, insert it into the multiplate, and enjoy.

## Customizing modules

### Gesture sensor

TODO

### OLED Display

You can use either 128x32 or 128x64 OLED modules. Just uncomment the corresponding `display` line in your main yml file.

### IR blaster

If you want to use the IR leds as a remote, please add any entity you want to use the IR transmitter with, to the main yml file. Use `ir_remote` as the transmitter id. Example:

```
climate:
  - platform: daikin
    name: ${device_name}-climate
    transmitter_id: ir_remote
```