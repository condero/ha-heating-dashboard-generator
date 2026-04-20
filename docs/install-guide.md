# Installation Guide: Heating Dashboard

## Prerequisites

- Home Assistant with all thermostats already connected and working
- Floors defined in HA (Settings > Areas & Zones > Floors) — optional but recommended for automatic grouping
- Browser access to the HA instance (no local/physical access needed)

## Overview

| Step | What                 | How                                     |
| ---- | -------------------- | --------------------------------------- |
| 1    | Install card-mod     | HACS, CDN download + upload, or CDN URL |
| 2    | Generate dashboard   | GUI — Developer Tools > Template        |
| 3    | Create dashboard     | GUI — Settings > Dashboards             |
| 4    | Paste dashboard YAML | GUI — Raw configuration editor          |

Everything is GUI only. card-mod is optional but recommended — without it, thermostat icons won't be color-coded by mode.

card-mod is needed because HA hardcodes thermostat icon colors based on the `hvac_action` attribute (heating/idle/off). When a thermostat is in heat or auto mode but currently idle (target temperature reached), HA shows a grey icon — the same as when it's turned off. card-mod overrides this by injecting CSS that colors icons based on the actual mode (heat/auto/off) instead.

---

## Step 1: Install card-mod

There are three ways to install card-mod. **Option A (local file) is recommended** because it keeps working even if external servers are down.

### Option A — Download and upload locally (recommended)

No HACS or GitHub account needed. The file is stored on your HA instance.

#### 1a — Download card-mod.js

1. Open this link in your browser: **`https://cdn.jsdelivr.net/gh/thomasloven/lovelace-card-mod@4.2.1/card-mod.js`**
2. Right-click the page and select **"Save as..."** (or press Ctrl+S / Cmd+S)
3. Save the file as **`card-mod.js`** to your computer

#### 1b — Upload to Home Assistant

You need a file manager. If you don't have one, install **File editor** from **Settings** > **Apps** first.

1. In the HA sidebar, click **File editor** — it opens in a new browser tab
2. Click the **folder icon** (top left) to open the file browser
3. If you don't see a **`www`** folder, click **"New Folder"** and name it `www`, then **restart Home Assistant** before continuing (Settings > three-dot menu > Restart Home Assistant)
4. Click on the **`www/`** folder to enter it
5. Click the **upload button** (top of the file browser — icon with an up arrow)
6. Select the **`card-mod.js`** file from your computer
7. The file should now appear in the `www/` folder

#### 1c — Add as a dashboard resource

1. Go to **Settings** > **Dashboards**
2. Click the **three-dot menu** (top right of the page)
3. Select **"Resources"**
4. Click **"Add resource"** (the + button)
5. Enter the URL: **`/local/card-mod.js`**
6. Select type: **JavaScript module**
7. Click **"Create"**
8. Refresh your browser

### Option B — Use CDN directly

No file download or upload required — HA loads card-mod directly from the internet.

1. Go to **Settings** > **Dashboards**
2. Click the **three-dot menu** (top right of the page)
3. Select **"Resources"**
4. Click **"Add resource"** (the + button)
5. Enter the URL: **`https://cdn.jsdelivr.net/gh/thomasloven/lovelace-card-mod@4.2.1/card-mod.js`**
6. Select type: **JavaScript module**
7. Click **"Create"**
8. Refresh your browser

**Note:** This requires internet access every time the dashboard loads. If the CDN is unreachable, the icon colors will not work.

### Option C — Install via HACS

If you already use [HACS](https://hacs.xyz/), this is the simplest option.

1. Open **HACS** in the HA sidebar
2. Click **"Explore & Download Repositories"**
3. Search for **"card-mod"**
4. Click on it, then click **"Download"**
5. Restart Home Assistant when prompted
6. Go to **Settings** > **Dashboards** > **three-dot menu** > **Resources**
7. Verify that `card-mod.js` appears in the resource list

---

## Step 2: Generate the dashboard YAML

1. Open **Developer Tools** > **Template**
2. Paste the entire content of `scripts/generate-dashboard.jinja` — the output appears automatically
3. Select all output and copy it

The template auto-detects:

- All climate entities and their battery sensors
- Floors (via `floor_id()`) — groups thermostats by floor
- Area names (via `area_name()`) — uses as display names
- Background colors — cycles through a preset palette

If you don't have floors defined, all thermostats will appear under a single "no_floor" group. You can either define floors in HA or edit the generated YAML to rearrange them.

---

## Step 3: Create the dashboard (GUI)

1. Go to **Settings** > **Dashboards**
2. Click **"Add Dashboard"** (top right)
3. Enter a title (e.g. `Heating Control`)
4. Click **"Create"**
5. The new empty dashboard opens

---

## Step 4: Paste the dashboard YAML (GUI)

1. In the new dashboard, click **Edit** (pencil icon, top right)
2. Click the **three-dot menu** (top right)
3. Select **"Raw configuration editor"**
4. Delete everything in the editor
5. Paste the template output (starts with `views:`)
6. Click **"Save"**
7. Close the raw editor, then click **Done**

The dashboard renders immediately with the correct icon colors.

---

## Verifying it works

Check these things:

| What to check                                 | Expected result                                 |
| --------------------------------------------- | ----------------------------------------------- |
| Thermostat set to heat, actively heating      | Orange icon                                     |
| Thermostat set to heat, target reached (idle) | Orange icon (same color — mode is still "heat") |
| Thermostat set to auto, currently idle        | Green icon                                      |
| Thermostat turned off                         | Grey icon                                       |
| Battery sensor unknown (device offline)       | Red icon                                        |
| Battery low (<20%)                            | Red icon (HA built-in)                          |
| Quick action buttons                          | Work when tapped                                |

---

## Language

The template defaults to English. To use German, change the first setting before pasting:

```jinja2
{%- set lang = 'de' %}
```

### Adding a language

Add a new entry to the `i18n` dictionary in the template. Copy an existing block and translate the values:

```jinja2
'fr': {
  'dashboard_title': 'Contrôle du chauffage',
  'all_sections': 'Tous les étages',
  'btn_off_name': 'Éteindre tous les radiateurs',
  'btn_off_action': 'Off',
  'btn_on_name': 'Restaurer la dernière valeur',
  'btn_on_action': 'On',
  'btn_auto_name': 'Mode auto (programme)',
  'btn_auto_action': 'Auto',
  'btn_comfort_name': 'Confort 21°C',
  'btn_comfort_action': '21°C',
  'battery': 'Batterie',
  'battery_offline': 'hors ligne',
},
```

## Colors

Each floor section gets a background color from a preset palette. The colors are [Material Design 200-level](https://materialui.co/colors) pastels — light enough to keep text readable.

The palette is ordered to maximize contrast between neighbors in a 2-column grid layout. No two adjacent panels (top, bottom, left, right) will have similar hues.

To change the colors, edit the `colors` list in the template:

```jinja2
{%- set colors = ['#FF8A65', '#90CAF9', '#CE93D8', ...] %}
```

You can use any hex color. The template cycles through the list, so if you have more floors than colors, it wraps around.

## Customizing the generated dashboard

The generated YAML is a starting point. Open the dashboard's Raw configuration editor to customize:

- **Section titles** — change `title:` in each section
- **Display names** — change `name:` on each entity row
- **Background colors** — change `color:` in each section
- **Quick actions** — remove entities from button target lists, or exclude entire sections
- **Comfort temperature** — change `temperature: 21` in the comfort buttons

---

## Updating later

**Dashboard changes (edit the YAML directly):**

1. Open the dashboard
2. Click **Edit** (pencil icon)
3. **Three-dot menu** > **"Raw configuration editor"**
4. Edit and save — applies immediately, no reload needed
5. Close the raw editor, then click **Done**

**Dashboard changes (regenerate from template):**

1. Go to **Developer Tools** > **Template**
2. Paste the template (edit it first if needed, e.g. change language)
3. Copy the output
4. Open the dashboard > **Edit** > **three-dot menu** > **Raw configuration editor**
5. Replace everything with the new output and save

**card-mod updates (Option A — local file):**

1. Download the latest release from the CDN (update the version number in the URL): `https://cdn.jsdelivr.net/gh/thomasloven/lovelace-card-mod@4.2.1/card-mod.js`
2. In **File editor**, navigate to `www/card-mod.js`
3. Delete the old file and upload the new one (or overwrite it)
4. Go to **Settings** > **Dashboards** > **three-dot menu** > **Resources**
5. Edit the card-mod resource and change the URL to `/local/card-mod.js?v=2` (increment this number each time you update — this busts the browser cache)
6. Click **"Update"**
7. Hard refresh your browser (Ctrl+Shift+R / Cmd+Shift+R)

**card-mod updates (Option C — HACS):**

1. Open **HACS**
2. Find **card-mod** in the list
3. Click **"Update"** if an update is available
4. Restart Home Assistant if prompted

---

## Troubleshooting

**Dashboard shows errors after pasting YAML:**

- Make sure you pasted the complete content starting with `views:`
- Check for accidental leading/trailing whitespace or missing lines
- Try a hard refresh (Ctrl+Shift+R / Cmd+Shift+R)

**Icons still grey for idle thermostats:**

- card-mod is required for this — verify it's installed:
  - Go to **Settings** > **Dashboards** > **three-dot menu** > **Resources**
  - You should see `card-mod.js` (or `/local/card-mod.js`) in the list
  - If not, follow Step 1 again
- Verify the dashboard YAML contains `card_mod:` entries on each thermostat entity
- Try a hard refresh (Ctrl+Shift+R / Cmd+Shift+R)
- Check browser console (F12 > Console) for JavaScript errors

**Browser console shows "Unexpected token <" or "SyntaxError":**

- You may have downloaded the wrong file — do NOT download from the GitHub master branch
- Use the CDN link from Step 1a: `https://cdn.jsdelivr.net/gh/thomasloven/lovelace-card-mod@4.2.1/card-mod.js`
- Re-download and re-upload the file

**Battery unknown icons still blue/default:**

- card-mod is required — the `card_mod:` entries on the conditional rows override the icon color
- Verify the dashboard YAML contains `card_mod:` on the "offline" conditional rows
- Try a hard refresh

**Colors not changing after dashboard update:**

- Confirm `state_color: true` is in the YAML
- Try a hard refresh

**Entities grouped under "no_floor":**

- Those entities don't have a floor assigned in HA
- Go to **Settings** > **Areas & Zones** > assign them to a floor
- Or edit the YAML to move them to the right section manually

**Display names look wrong:**

- Entities probably don't have areas assigned
- Edit the `name:` fields in the YAML to use simple room names
