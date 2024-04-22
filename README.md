# Wemos Multiplate
(placeholder name)

Multifunctional device with esphome compatible sensors and interactive tools to improve your smart home experience.

![front](images/2-2-front.png)

## Esphome compiling and flashing
- install esphome on your machine. https://esphome.io/guides/installing_esphome.html
- duplicate the `secrets_sample.yaml` file, rename it to `secrets.yaml`, and fill your wifi SSID data in.
- duplicate `esphome/multiplate.yml` file, rename it to your liking.
- open the duplicated file and edit `device_name` to your liking, and comment/uncomment the modules you want to use.
  - note that `gesture_sensor` and `light_sensor` cannot be used at the same time at the moment.
  - if you want to use `ir_remote`, please open `esphome/modules/ir_remote.yml` and add any entity you want, like climate etc.
  - if you want to use `display`, please open `esphome/modules/display.yml` and comment out the model you are not using.
- connect the Wemos D1 mini via USB and flash it using the `esphome run` command.
- disconnect the Wemos D1 mini, insert it into the multiplate, and enjoy.