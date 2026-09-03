# A finally working and cheap fridge camera!
If you found this useful, please star this repo.

An ESPHome configuration for an ESP32-CAM tailored for monitoring a refrigerator interior. Includes video streaming, a toggleable flashlight, and a reed-switch door sensor.

You can mount it inside or outside the fridge, but... Just do it how you want...

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

This blueprint takes a snapshot when the door opens, sends it to an AI
vision model, and creates or updates one To-Do item per recognized food
item: name, a short description of quantity/condition, and an estimated
bounding box (for [fridge-card](https://github.com/jan-tdy/fridge-card)'s
detection-frame overlay). Items already in the list are matched by name,
so an expiration/"best before" date you set by hand stays intact across
scans. So does a **Brand** you set on the card - the AI never writes to
that field - and a detection frame you **drew by hand** on the card; an
AI-estimated frame you haven't corrected still refreshes normally on
each scan. Items no longer detected are left alone - remove them
yourself once you've used them up.

The AI is also told which item names were already used in a previous
scan (with their last known box location) and asked to reuse an exact
name when it's plausibly the same physical item — e.g. if you rename
"Palacinka" to "Praženica" on the card, the next scan is nudged to say
"Praženica" again instead of re-guessing. This is **not** real training —
each analysis is a fresh, stateless request with no memory of past
runs — it's just context added to that one prompt, so it's a best-effort
nudge, not a guarantee; the AI can still occasionally invent a different
name.

The **AI Output Language** blueprint input switches the item
names/descriptions between English and Slovenčina; the structural
markup (confidence scores, box coordinates) is unaffected.

### Prerequisites
1. **AI Integration:** You must have an extended AI conversation or task integration configured (like `Google Generative AI` or `OpenAI Conversation`) that provides the `ai_task.generate_data` action.
2. **Folder Creation:** Ensure the folder `/config/www/fridge/` exists in your Home Assistant configuration directory to allow the camera snapshot to save correctly.
3. **To-Do List:** Create a dedicated To-Do list in Home Assistant named `Fridge Contents` (or similar).

### Installation
Click the button below or copy your raw file link directly into the **Settings > Automations & Scenes > Blueprints > Import Blueprint** menu in Home Assistant.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fjan-tdy%2Ffridge-core%2Fblob%2Fmain%2Fautomation.yaml)

## Companion card

[jan-tdy/fridge-card](https://github.com/jan-tdy/fridge-card) is the
Lovelace UI half of this project: a custom card that shows the latest
snapshot (with a config option to correct a crooked camera mount) and the
To-Do items this blueprint creates as a plain, editable list (name,
description, brand, expiration date - no checkboxes), an optional overlay
of the bounding boxes this blueprint estimates (editable/drawable by
hand), plus quick controls for the
light, door status, live camera view and re-running this automation.
Point its `todo_entity` at the same To-Do list configured above.

If you found this useful, please star both repos!
