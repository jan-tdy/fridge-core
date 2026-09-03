# A finally working and cheap fridge camera!

An ESPHome configuration for an ESP32-CAM tailored for monitoring a refrigerator interior. Includes video streaming, a toggleable flashlight, and a reed-switch door sensor.

## Hardware Setup
* **Camera:** AI Thinker ESP32-CAM module.
* **Flashlight:** Built-in high-power LED connected to `GPIO4`.
* **Door Sensor:** Simple 2-wire magnetic reed switch connected between `GPIO13` and `GND` (no external resistors needed due to `INPUT_PULLUP`).

## How to Use
1. Copy the contents of `esphome-config.yaml` into your ESPHome environment.
2. Edit the top `substitutions:` block to match your preferred names and area.
3. Ensure your local `secrets.yaml` contains the required keys: `wifi_ssid`, `wifi_password`, and `fridgecam_api_key`.
4. Flash the device.
5. Create a to-do list in Home Assistant named `Fridge Contents`.
6. Create a new automation in Home Assistant and switch to YAML edit mode.
7. Copy the contents of `automation.yaml` to the new automation.
8. Check that all entities in the automation are correct; if not, correct them for your setup.

If you found this useful, please star this repo! Also take a look at the card for it: www.github.com/jan-tdy/fridge-card
