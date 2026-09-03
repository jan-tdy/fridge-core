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
5. Home Assistant AI Automation Setup

This blueprint uses local storage, a To-Do list, and an AI integration to parse images of your fridge contents in Slovak.

### Prerequisites
1. **AI Integration:** You must have an extended AI conversation or task integration configured (like `Google Generative AI` or `OpenAI Conversation`) that provides the `ai_task.generate_data` action.
2. **Folder Creation:** Ensure the folder `/config/www/fridge/` exists in your Home Assistant configuration directory to allow the camera snapshot to save correctly.
3. **To-Do List:** Create a dedicated To-Do list in Home Assistant named `Fridge Contents` (or similar).

### Installation
Click the button below or copy your raw file link directly into the **Settings > Automations & Scenes > Blueprints > Import Blueprint** menu in Home Assistant.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fjan-tdy%2Ffridge-core%2Fblob%2Fmain%2Fautomation.yaml)


If you found this useful, please star this repo! Also take a look at the card for it: www.github.com/jan-tdy/fridge-card
