# Wemos Multiplate

Wemos Multiplate is a modular, ESPHome-powered front plate for Wemos D1 mini and Wemos S2 mini development boards. It combines presence sensing, environmental telemetry, status lighting, and user inputs into a single wall-friendly enclosure.

![front](images/2-2-front.png)

## Highlights
- Works with Wemos D1 mini (ESP8266) and Wemos S2 mini (ESP32-S2); the S2 mini is strongly recommended if you want every module.
- Integrated 128x64 SSD1306 OLED display with customizable pages.
- Hi-Link LD2410C 24 GHz mmWave presence sensor with optional Bluetooth tuning.
- SHT31-D temperature and humidity probe plus optional BH1750 ambient light and APDS9960 gesture/proximity sensors.
- Two WS2812 addressable LEDs, a piezo buzzer, IR transmitter/receiver, and a header for an external relay module.
- ESPHome package-based configuration lets you enable only the hardware you soldered.

## Repository Layout
- `esphome/` – modular ESPHome configuration split by board (`modules_d1mini/`, `modules_s2mini/`) and shared components (`modules_common/`). Sample automations are under `esphome/automations/`.
- `kicad/wemos-esphome-shield/` – KiCad project, gerbers, STEP models, and release archives for the PCB.
- `images/` – renders and photos used by this README.

## Hardware Overview
**Base components**
- Wemos D1 mini or Wemos S2 mini dev board (USB-C S2 mini preferred).
- 128x64 I2C OLED display (SSD1306).
- Hi-Link LD2410C mmWave presence sensor.
- Sensirion SHT31-D temperature and humidity sensor.
- Two WS2812 (5 V) addressable LEDs.
- Piezo buzzer with drive transistor.
- TSOP-style IR receiver and 940 nm IR LED pair.

**Optional add-ons**
- BH1750 illuminance sensor.
- APDS9960 gesture/proximity sensor.
- Three front buttons (requires the S2 mini pinout).
- Header for an external 5 V relay module or other accessories.

PCB fabrication files for the latest revision live in `kicad/wemos-esphome-shield/releases/`.

## Getting Started with ESPHome

1. **Install ESPHome**  
   Follow the [ESPHome installation guide](https://esphome.io/guides/installing_esphome.html) (CLI or Home Assistant add-on). CLI examples below assume `pipx install esphome`.

2. **Create secrets**  
   Copy the sample secrets file and edit it with your Wi-Fi and fallback hotspot credentials:
   ```bash
   cp esphome/modules_s2mini/secrets_sample.yaml esphome/secrets.yaml
   ```
   The same `secrets.yaml` is used by both board profiles.

3. **Pick a base profile**  
   - `esphome/multiplate_s2mini.yml` — ESP32-S2 build with every module enabled.  
   - `esphome/multiplate_d1mini.yml` — ESP8266 build with a reduced feature set. Comment out any modules you did not solder to stay within memory limits.

4. **Enable or disable modules**  
   Each module is loaded via the `packages:` section. Comment out a line to disable the corresponding hardware. For example, to skip the gesture sensor:
   ```yaml
   # gesture_sensor: !include modules_common/gesture_sensor.yml
   ```

5. **Build and flash**  
   Connect the board over USB and run:
   ```bash
   esphome run esphome/multiplate_s2mini.yml --device /dev/ttyUSB0
   ```
   After the first successful flash you can go OTA:
   ```bash
   esphome run esphome/your-device-name.yml
   ```

6. **Adopt in Home Assistant**  
   The node exposes all sensors, lights, switches, and buttons via the ESPHome API. Home Assistant will prompt to adopt the new device and automatically create entities.

## Module Reference

| Module                                 | File                                                                        | Notes |
|----------------------------------------|-----------------------------------------------------------------------------|-------|
| Core board setup & Wi-Fi               | `modules_d1mini/common.yml`, `modules_s2mini/common.yml`                    | Handles board selection, Wi-Fi credentials, API, OTA. |
| OLED display, pages, and fonts         | `modules_common/display.yml`, `font/retrogaming.ttf`                        | Provides readout and trend pages with a 4 h temperature graph. |
| LD2410C mmWave presence                | `modules_d1mini/motion_sensor.yml`, `modules_s2mini/motion_sensor.yml`      | Exposes presence, moving/still targets, distance, and Bluetooth control. |
| Temperature & humidity (SHT31-D)       | `modules_common/temp_sensor.yml`                                           | Includes a median filter and linear calibration datapoints. |
| Ambient light (BH1750)                 | `modules_common/light_sensor.yml`                                          | Optional, comment out if the sensor is not populated. |
| Gesture & proximity (APDS9960)         | `modules_common/gesture_sensor.yml`                                        | Optional, provides four gesture binary sensors and calibrated proximity. |
| RGB status LEDs                        | `modules_d1mini/rgb_leds.yml`, `modules_s2mini/rgb_leds.yml`                | Two WS2812 LEDs mapped in the sample automations. |
| Buzzer                                 | `modules_d1mini/buzzer.yml`, `modules_s2mini/buzzer.yml`                    | Template buttons trigger predefined alert patterns. |
| IR transmitter/receiver                | `modules_d1mini/ir_remote.yml`, `modules_s2mini/ir_remote.yml`             | Exposes `ir_remote` transmitter ID and raw receiver for learning codes. |
| Relay header                           | `modules_d1mini/relay.yml`, `modules_s2mini/relay.yml`                      | Optional GPIO-controlled 5 V relay output. |
| Front buttons (S2 mini only)           | `modules_s2mini/buttons.yml`                                               | Three debounced GPIO inputs. |
| Automations (display & LEDs)           | `automations/display_motion.yml`, `automations/motion_status_leds.yml`      | Opt-in template switches to toggle display blanking and LED indicators. |

## Built-in Automations
- `display_motion` keeps the OLED blank until motion is detected, then shows the readout page.
- `motion_status_leds` maps moving and still target detection to the two WS2812 LEDs.
Enable or disable these from the `packages:` block just like the hardware modules.

## Tuning the LD2410C mmWave Sensor
1. Enable the Bluetooth switch entity (`*-ld2410-bluetooth`) exposed by ESPHome.
2. Use the official Android configuration app: <https://play.google.com/store/apps/details?id=com.hlk.hlkradartool>.
3. Connect to the sensor, enter Engineering Mode, and adjust the eight detection gates to match your room layout. Lower values increase sensitivity.
4. Apollo Automation maintains a detailed tuning guide: <https://wiki.apolloautomation.com/books/msr-1/page/how-to-tune-mmwave-using-home-assistant>.

Keeping the heavy tuning UI out of ESPHome reduces load on the D1 mini and shortens boot times.

## Using the IR Remote
Add climate, media, or switch components to your ESPHome profile and reference the shared `ir_remote` transmitter and `ir_rx` receiver IDs. Example:

```yaml
climate:
  - platform: daikin
    name: ${device_name}-climate
    transmitter_id: ir_remote
    receiver_id: ir_rx
```

Use the `remote_receiver` in learning mode (`dump: raw`) to capture codes from your existing remote.

## Manufacturing Files
- Latest gerber sets: `kicad/wemos-esphome-shield/gerber/`
- STEP models for enclosure work: `kicad/wemos-esphome-shield/step/`
- Release bundles (gerbers, BOM, pick-and-place): `kicad/wemos-esphome-shield/releases/`

Send the gerber ZIP to your PCB manufacturer of choice, then populate the board according to the silkscreen and BOM in the release archive.

---

Questions, improvements, or pull requests are welcome!

