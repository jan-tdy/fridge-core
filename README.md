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
item: name plus separate quantity, condition, confidence and note fields,
and an estimated bounding box (for
[fridge-card](https://github.com/jan-tdy/fridge-card)'s detection-frame
overlay). Items already in the list are matched by name, so an
expiration/"best before" date you set by hand stays intact across scans.
So does anything you edit on the card: quantity, condition, note, a
**Brand** (which the AI never writes to at all) and a detection frame you
**drew by hand** all stay exactly as you set them on the next scan
instead of being overwritten by a fresh AI guess. Confidence is always
the AI's latest estimate, since only the AI ever sets it. Items no
longer detected are left alone - remove them yourself once you've used
them up. An item marked eaten on the card is reactivated - not
duplicated - if a later scan still recognizes it under the same name;
it was evidently still there.

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

### Optional: saved snapshot history

fridge-card's **Save** and ◀ ▶ **Latest** controls let you keep a copy of
the current photo and browse back through previously saved ones. This
needs one `shell_command` added to your Home Assistant
`configuration.yaml` - the card is just a browser page, so it has no way
to copy a file on the HA server by itself:

```yaml
shell_command:
  fridge_save_snapshot: >-
    sh -c 'ts=$(date +%s);
    mkdir -p /config/www/fridge/history;
    cp /config/www/fridge/fridge_latest.jpg /config/www/fridge/history/fridge_$ts.jpg;
    echo $ts >> /config/www/fridge/history/manifest.txt'
```

Restart Home Assistant (or reload YAML) after adding it, then set the
card's `snapshot_service` option to `shell_command.fridge_save_snapshot`.
Pressing **Save** copies whatever `fridge_latest.jpg` currently holds -
not a fresh camera capture - into `history/` under a timestamped
filename, and appends that timestamp to `history/manifest.txt`; the card
reads that plain text file directly to know what's browsable. Adjust the
paths in the command if your `image_path` isn't the default
`/local/fridge/fridge_latest.jpg`. This is entirely optional - without
it, the card just shows the live photo as before.

## Companion card

[jan-tdy/fridge-card](https://github.com/jan-tdy/fridge-card) is the
Lovelace UI half of this project: a custom card that shows the latest
snapshot (with a config option to correct a crooked camera mount, an
optional way to save and browse back through older snapshots - see
above - and a refresh button to force-reload it) and the To-Do items
this blueprint creates as a plain, editable list - name, quantity,
condition, an AI confidence readout, note, brand, expiration date, each
its own field, plus a checkbox to mark an item eaten (tucked into a
collapsed "Eaten" section instead of deleted outright) - an optional
overlay of the bounding boxes this blueprint estimates
(editable/drawable by hand), plus quick controls for the light, door
status, live camera view and re-running this automation. Point its
`todo_entity` at the same To-Do list configured above.

If you found this useful, please star both repos!
