# k-flow-card (dinowuk edition)

**Animated Home Assistant energy-flow card — fork of [thekhan1122/k-flow-card](https://github.com/thekhan1122/k-flow-card) by Khan Automation, extended and maintained by [dinowuk](https://github.com/dinowuk).**

This fork keeps 100% of the original's features (battery, EV, dual-battery, extra PV strings, full visual editor) and adds a set of new, fully optional, fully configurable extras aimed at solar-only (no battery) setups and multi-language dashboards — while staying completely backward compatible with the original config.

<img width="1708" height="2520" alt="k-flow-card" src="https://github.com/user-attachments/assets/e4c54570-5b9e-43c4-9326-b44d4105a5d1" />
<img width="1108" height="1688" alt="k-flow-card" src="https://github.com/user-attachments/assets/6914d4ab-daa6-432a-983d-6abac15a1cac" />
<img width="1114" height="1744" alt="k-flow-card" src="https://github.com/user-attachments/assets/ce97703f-ed71-4ab9-a79d-8d7cf3bcd3f6" />

---

## Overview

`k-flow-card` is a fully custom Home Assistant Lovelace card that renders a live, animated energy-flow diagram for a solar inverter system, with optional battery storage and EV charger. It is self-contained in a single JavaScript file — no dependencies, no other HACS card required beyond loading the resource.

The card combines an SVG energy-flow canvas (sun arc, animated flow paths, inverter, battery, grid, home, EV nodes) with an HTML stat panel showing real-time telemetry. Everything updates live via the standard `hass` setter.

---

## What's new in this fork

Everything below is **off by default** and fully backward compatible — existing configs from the original card work unchanged.

- **Editable text labels** — card title, PV string 1/2 labels, home/load label, battery power-bar label, charge/discharge tile label, remaining-capacity tile label, and the three battery status words (idle / charging / discharging), plus the "Till" ETA prefix. All editable straight in the visual editor, no YAML needed. Handy for translating the card into any language.
- **Production Start / End Times** *(new optional section, toggle in editor)* — shows the time the inverter's first and most recent production crossing of the day happened, driven by two `timestamp` entities you point at (e.g. two small trigger-based template sensors in Home Assistant).
- **Month / Year Summary** *(new optional section, toggle in editor)* — a collapsible block showing monthly and yearly totals for PV production, home consumption, grid export and grid import, each pointed at your own entities (e.g. `utility_meter` helpers with `cycle: monthly` / `cycle: yearly`).
- **Battery section bug fix** — in the original, the six battery stat tiles (Cell Temp, BMS Temp, Min/Max Cell, Batt Discharge, Total PV Gen) and the Endurance tile were always rendered regardless of the `_show_battery` toggle. They're now correctly hidden when `_show_battery: false`, so solar-only installs get a clean card with zero battery clutter.

See [Configuration Reference](#configuration-reference) below for the full list of new keys.

---

## Features (inherited from upstream)

- Animated S-curve energy flow paths (solar → inverter → battery / grid / home / EV)
- Live sun position tracking along an arc based on `sun.sun` elevation
- Battery SOC colour coding with dynamic charge/discharge indicators
- Dual battery support (primary + secondary, stacked layout)
- EV / car charger node with live state, SOC, and ETA
- Extra PV strings (PV3 / PV4) with combined toggle
- System limits panel (battery Ah/Wh, inverter max, PV max)
- Full visual editor with section toggles — no YAML required for setup
- Per-row custom label and entity overrides for all 6 battery stat tiles
- Fully dark-themed, mobile-friendly

---

## Installation

### Method 1 — HACS (Recommended)

1. In HACS, go to **Frontend → ⋮ → Custom repositories**
2. Paste: `https://github.com/dinowuk/k-flow-card` (without `.git`)
3. Category: **Lovelace**
4. Click the **+ Explore & Download Repositories** button.
5. Search for **k-flow-card**.
6. Click **Download** — the resource is added automatically.
7. **Hard refresh** your browser (`Ctrl + Shift + R` / `Cmd + Shift + R`).
8. Open the visual editor to configure entities.

> HACS handles resource registration automatically. No manual resource entry needed.

---

### Method 2 — Manual

1. Copy `k-flow-card.js` to your HA config folder:
   ```
   /config/www/k-flow-card.js
   ```

2. Register as a Lovelace resource:
   *(Settings → Dashboards → Resources → Add)*
   ```yaml
   url: /local/k-flow-card.js
   type: module
   ```

3. Add to a dashboard view:
   ```yaml
   type: custom:k-flow-card
   ```

4. Open the visual editor to configure entities.

---

## Configuration Reference

All keys are configured through the visual editor. The YAML equivalents are listed below for reference or manual setup.

### New in this fork

| Key | Default | Description |
|---|---|---|
| `card_title` | `Energy Flow` | Card header title |
| `pv1_label` | `PV1` | PV string 1 label (SVG) |
| `pv2_label` | `PV2` | PV string 2 label (SVG) |
| `home_label` | `Home` | Home/load label (SVG, near house node) |
| `label_pwr` | `Pwr` | Battery power-bar label |
| `label_chg_dis` | `Chg / Dis` | Inverter row "Chg/Dis" tile label |
| `label_remaining` | `Remaining` | Inverter row "Remaining" tile label |
| `label_idle` | `IDLE` | Battery status badge — idle |
| `label_charging` | `CHG` | Battery status badge — charging |
| `label_discharging` | `DISCHG` | Battery status badge — discharging |
| `label_till_prefix` | `Till` | Prefix for the endurance ETA timestamp |
| `_show_production_times` | `false` | Toggle the Production Start/End Times section |
| `production_start_label` | `Start` | Label for the start tile |
| `production_end_label` | `End` | Label for the end tile |
| `production_start_entity` | `''` | `timestamp` entity — first production crossing today |
| `production_end_entity` | `''` | `timestamp` entity — most recent production crossing today |
| `_show_month_year` | `false` | Toggle the Month/Year Summary section |
| `month_pv_entity` | `''` | Monthly PV production (kWh) |
| `month_load_entity` | `''` | Monthly load consumption (kWh) |
| `month_export_entity` | `''` | Monthly grid export (kWh) |
| `month_import_entity` | `''` | Monthly grid import (kWh) |
| `year_pv_entity` | `''` | Yearly PV production (kWh) |
| `year_load_entity` | `''` | Yearly load consumption (kWh) |
| `year_export_entity` | `''` | Yearly grid export (kWh) |
| `year_import_entity` | `''` | Yearly grid import (kWh) |

> Tip: `production_start_entity` / `production_end_entity` and the month/year entities are meant to be fed by your own Home Assistant helpers — see the "Recipes" section below for a ready-to-use `configuration.yaml` snippet.

### Core / Solar

| Key | Default | Description |
|---|---|---|
| `inverter_name` | `''` | Label shown in the inverter node |
| `pv1_power` | `sensor.goodwe_pv1_power` | PV string 1 power (W) |
| `pv2_power` | `sensor.goodwe_pv2_power` | PV string 2 power (W) |
| `pv3_power` | `''` | PV string 3 — optional, enable via Extra PV toggle |
| `pv4_power` | `''` | PV string 4 — optional, enable via Extra PV toggle |
| `pv_total_power` | `sensor.goodwe_pv_power` | Total PV power (W) |
| `pv_max_power` | `7500` | Max PV power for bar scaling (W) |
| `today_pv` | `sensor.goodwe_today_s_pv_generation` | Today's PV generation (kWh) |
| `total_pv_gen_entity` | `sensor.goodwe_total_pv_generation` | Lifetime PV generation (kWh) |
| `inv_temp` | `sensor.goodwe_inverter_temperature_module` | Inverter temperature |
| `today_batt_chg` | `sensor.goodwe_today_battery_charge` | Today battery charge (kWh) |
| `today_load` | `sensor.goodwe_today_load` | Today load (kWh) |
| `sun` | `sun.sun` | Sun entity for arc position |

### Grid

| Key | Default | Description |
|---|---|---|
| `grid_active_power` | `sensor.goodwe_active_power` | Grid active power (W) |
| `grid_import_energy` | `sensor.goodwe_today_energy_import` | Today grid import (kWh) |
| `grid_export_energy` | `''` | Today grid export (kWh) — optional |
| `grid_power_alt` | `sensor.grid_phase_a_power` | Alternate grid power sensor |
| `invert_grid_power` | `false` | Invert sign — enable if positive = exporting |
| `consump` | `sensor.goodwe_house_consumption` | House consumption (W) |

### Primary Battery

| Key | Default | Description |
|---|---|---|
| `_show_battery` | `true` | Show primary battery section (icon, Pwr bar, stat tiles, endurance, Chg/Dis + Remaining tiles) |
| `battery_soc` | `sensor.jk_soc` | Battery state of charge (%) |
| `battery_power` | `sensor.jk_power` | Battery power (W) |
| `battery_current` | `sensor.jk_current` | Battery current (A) |
| `battery_voltage` | `sensor.jk_voltage` | Battery voltage (V) |
| `battery_temp1` | `sensor.jk_temp1` | Cell temp probe 1 |
| `battery_temp2` | `sensor.jk_temp2` | Cell temp probe 2 |
| `battery_mos` | `sensor.jk_mos` | BMS MOS temperature |
| `battery_min_cell` | `sensor.jk_cellmin` | Min cell voltage |
| `battery_max_cell` | `sensor.jk_cellmax` | Max cell voltage |
| `batt_dis` | `sensor.goodwe_today_battery_discharge` | Today discharge (kWh) |
| `battery_full_ah` | `314` | Battery capacity (Ah) |
| `battery_full_wh` | `16076` | Battery capacity (Wh) |
| `goodwe_battery_soc` | `sensor.goodwe_battery_state_of_charge` | Fallback SOC |
| `goodwe_battery_curr` | `sensor.goodwe_battery_current` | Fallback current |
| `invert_battery_power` | `false` | Invert sign — enable if positive = discharging |

> **No battery?** Set `_show_battery: false` (uncheck "Primary Battery" in the editor) and the Pwr bar, status badge, all six stat tiles, the Endurance tile, and the Chg/Dis + Remaining inverter-row tiles all disappear — the card reflows to a clean 2-column inverter row automatically.

### Secondary Battery

| Key | Default | Description |
|---|---|---|
| `_show_battery2` | `false` | Enable secondary battery (chip toggle) |
| `battery2_soc` | `''` | Secondary SOC (%) |
| `battery2_power` | `''` | Secondary power (W) |
| `battery2_current` | `''` | Secondary current (A) |
| `battery2_voltage` | `''` | Secondary voltage (V) |
| `battery2_mos` | `''` | Secondary BMS temperature |

### EV / Car Charger

| Key | Default | Description |
|---|---|---|
| `_show_ev` | `false` | Enable EV section (chip toggle) |
| `charger_state` | `''` | Charger state entity (string: `charging`, `completed`, etc.) |
| `charger_power` | `''` | Charger power (W) |
| `charger_current` | `''` | Charger current (A) |
| `charger_soc` | `''` | Car battery SOC (%) |
| `charger_eta` | `''` | Charge ETA in minutes — optional |
| `charger_battery_capacity_wh` | `''` | EV battery capacity (Wh) |

### System Limits

| Key | Default | Description |
|---|---|---|
| `_show_limits` | `false` | Show limits section (chip toggle) |
| `inverter_max_power` | `6000` | Inverter max power for bar scaling (W) |

### Labels (custom entity overrides for battery stat tiles)

| Key | Default | Description |
|---|---|---|
| `_labels_custom_entities` | `false` | Enable Labels section (chip toggle) |
| `label_cell_temp_minmax` | `CELL TEMP MIN/MAX` | Tile label — cell temp |
| `label_bms_temp` | `BMS TEMP` | Tile label — BMS temp |
| `label_min_cell` | `Min Cell` | Tile label — min cell voltage |
| `label_max_cell` | `Max Cell` | Tile label — max cell voltage |
| `label_batt_dis` | `Batt Dis.` | Tile label — battery discharge |
| `label_total_pv_gen` | `TOTAL PV GEN.` | Tile label — total PV generation |
| `label_entity_cell_temp` | `''` | Override entity for cell temp tile |
| `label_entity_bms_temp` | `''` | Override entity for BMS temp tile |
| `label_entity_min_cell` | `''` | Override entity for min cell tile |
| `label_entity_max_cell` | `''` | Override entity for max cell tile |
| `label_entity_batt_dis` | `''` | Override entity for batt dis tile |

> **Entity override rule:** An entity picker in the Labels section activates only when its corresponding label text has been changed from the default. Once active, the matching picker in the Battery section is locked with an "Overridden by Labels" veil to prevent duplication.

---

## Recipes

### Monthly / yearly totals from a power sensor (for the Month/Year Summary section)

If you only have instantaneous power sensors (W) for grid import/export and load, you can derive monthly/yearly kWh totals with two built-in Home Assistant helpers — no extra integration needed:

```yaml
sensor:
  - platform: integration
    source: sensor.my_grid_export_power   # W
    name: "Grid Export Energy"
    unique_id: grid_export_energy
    unit_prefix: k
    round: 3
    method: left

utility_meter:
  my_month_grid_export:
    source: sensor.grid_export_energy
    cycle: monthly
  my_year_grid_export:
    source: sensor.grid_export_energy
    cycle: yearly
```

Repeat the same pattern (integration → utility_meter with `cycle: monthly` / `cycle: yearly`) for PV production and load consumption, then point the card's `month_*_entity` / `year_*_entity` fields at the resulting `utility_meter` sensors.

### Production start / end timestamps

```yaml
template:
  - trigger:
      - trigger: numeric_state
        entity_id: sensor.my_pv_power
        above: 10
      - trigger: time
        at: "00:00:00"
    sensor:
      - name: "Production Start"
        unique_id: production_start
        device_class: timestamp
        state: >
          {% if trigger.platform == 'time' %}
            {{ none }}
          {% else %}
            {{ now() }}
          {% endif %}
  - trigger:
      - trigger: numeric_state
        entity_id: sensor.my_pv_power
        below: 10
      - trigger: time
        at: "00:00:00"
    sensor:
      - name: "Production End"
        unique_id: production_end
        device_class: timestamp
        state: >
          {% if trigger.platform == 'time' %}
            {{ none }}
          {% else %}
            {{ now() }}
          {% endif %}
```

Point `production_start_entity` / `production_end_entity` at `sensor.production_start` / `sensor.production_end`.

---

## Visual Editor Sections

| Section | Toggle | Description |
|---|---|---|
| General | — | Inverter name, card title, PV/home labels |
| Labels | `+ Enable` chip | Rename stat tiles; per-row entity overrides |
| Solar | — | PV1, PV2 entities |
| Extra PV Strings | `+ Enable` chip | PV3, PV4 |
| Solar Extras | — | Totals, temperatures, today stats |
| Grid | — | Grid power, import/export, consumption |
| Primary Battery | `+ Enable` chip | Full BMS telemetry, Pwr bar, badge, and tile text overrides |
| Secondary Battery | `+ Enable` chip | Second pack |
| System Limits | `+ Enable` chip | Capacity and power limits |
| EV / Car Charger | `+ Enable` chip | Charger state, SOC, ETA |
| Production Start/End Times | `+ Enable` chip | *(new)* Timestamps for first/last production crossing today |
| Month / Year Summary | `+ Enable` chip | *(new)* Monthly and yearly PV/load/grid totals |

---

## Colour Logic

| Metric | Thresholds |
|---|---|
| **SOC** | ≤25% red · ≤50% orange · ≤75% blue · >75% green |
| **Cell Temp** | ≤15°C blue · ≤35°C green · ≤45°C orange · >45°C red |
| **Cell Voltage** | <3.0V red · <3.1V orange · <3.4V yellow · ≤3.65V green · >3.65V red |
| **Inverter / Env Temp** | ≤25°C green · ≤45°C orange · >45°C red |

---

## File Structure

```
k-flow-card.js
│
├── class KFlowCardEditor   (visual editor — HTMLElement, shadow DOM)
│   ├── _render()           builds editor sections
│   ├── makeSection()       collapsible section with optional chip toggle
│   ├── picker()            ha-selector entity picker
│   ├── textField()         ha-textfield input
│   ├── numberField()       ha-textfield numeric input
│   ├── switchRow()         pill toggle (used for invert flags)
│   ├── labelRow()          text field + conditionally-enabled entity picker
│   └── pickerMaybeDisabled() picker with override veil overlay
│
└── class KFlowCard         (main card — HTMLElement, shadow DOM)
    ├── setConfig()         merges config with stub defaults, triggers static build
    ├── _buildStaticSVG()   renders full SVG + HTML stat panel (called once per config)
    ├── _updateDynamic()    updates all live values, colours, animations (called on every hass update)
    ├── _val()              safe numeric entity reader
    ├── _strVal()           safe string entity reader
    ├── _socColor()         SOC → hex colour
    ├── _cellTempColor()    temperature → hex colour
    ├── _cellVoltColor()    cell voltage → hex colour
    ├── _tempColor()        general temperature → hex colour
    ├── _remCapColor()      remaining capacity → hex colour
    └── _fmtTill()          hours → "Till HH:MM" or "in Xh Ym" string
```

---

## Notes

- The card uses shadow DOM — custom CSS from themes does not penetrate the card. All colours are hardcoded or driven by entity values.
- Config keys prefixed with `_` (e.g. `_show_battery`) are editor-only boolean toggles — they control visibility but are stored in the card config YAML.
- When installed via HACS, the resource is registered automatically. When installed manually, register as `type: module`.

---

## Troubleshooting

### Card does not appear / shows "Custom element doesn't exist"

- Confirm the resource is registered: **Settings → Dashboards → Resources**. You should see `/hacsfiles/k-flow-card/k-flow-card.js` (HACS) or `/local/k-flow-card.js` (manual) with type `JavaScript Module`.
- Hard refresh the browser: `Ctrl + Shift + R` (Windows/Linux) or `Cmd + Shift + R` (Mac).
- If using the mobile app, clear app cache or force-close and reopen.
- If installed manually, confirm the file is at `/config/www/k-flow-card.js` — not inside a subfolder.

---

### Visual editor is blank or fails to load

- Open browser DevTools (`F12`) → Console tab. Look for any red errors referencing `k-flow-card`.
- Ensure no other version of `k-flow-card.js` is registered as a duplicate resource. Go to Resources and remove any stale entries.
- Try clearing the HA frontend cache: **Developer Tools → Template** → reload page.

---

### Entities show `--` or do not update

- Open **Developer Tools → States** and confirm the entity ID exists and has a valid numeric state (not `unavailable` or `unknown`).
- Entity IDs are case-sensitive. Check for typos in the editor.
- The card skips `unavailable` and `unknown` states by design — the tile will show `--` until the entity returns a valid value.

---

### Flow animations not showing

- The card requires `sun.sun` to be present for the sun arc. If it is missing, the sun node will not animate but the rest of the card will function normally.
- Flow path animations are driven by power values. A path will only animate when its corresponding power reading is above zero.
- If all flows are static, check that your inverter entities are returning live values and not stale/unavailable states.

---

### Battery section is missing

- The Primary Battery section requires `_show_battery: true`. In the visual editor, click the **+ Enable** chip next to **Primary Battery**.
- Secondary Battery, EV, Extra PV Strings, System Limits, and Labels sections each have their own **+ Enable** chip — they are hidden by default.

---

### Endurance tile shows `--` or incorrect time

- The endurance calculation requires `battery_full_ah` (capacity in Ah) and `battery_current` to be set and returning valid values.
- If the battery is neither charging nor discharging (current ≈ 0), the tile will show `--` as no meaningful estimate is possible.
- Ensure `battery_full_ah` in **System Limits** matches your actual battery capacity.

---

### Labels section entity pickers are greyed out

- The **Labels** section must be enabled first via the **+ Enable** chip in the section header.
- Once enabled, each entity picker unlocks **only after you change that row's label text** away from its default. This is by design — it prevents accidental entity overrides.
- To unlock the Cell Temp entity picker, for example, change the label text from `CELL TEMP MIN/MAX` to anything else.

---

### After update via HACS, card looks wrong or broken

1. Hard refresh the browser (`Ctrl + Shift + R`).
2. If the issue persists, go to **Settings → Dashboards → Resources**, delete the k-flow-card entry, then re-add it (HACS will re-register it on the next HA restart).
3. Restart Home Assistant and hard refresh again.

---

### Reporting a bug

Please open an issue on [this repo](https://github.com/dinowuk/k-flow-card/issues) with:
- Home Assistant version
- k-flow-card version (visible in browser DevTools console on load)
- Browser console errors (screenshot or copy-paste)
- Relevant section of your card YAML config (remove sensitive entity names if needed)

---

## Credits & License

This is a fork of [thekhan1122/k-flow-card](https://github.com/thekhan1122/k-flow-card) ("Khan Automation"), which remains the original author of the base card, its animated SVG engine, and the visual editor framework. All credit for the original design and the vast majority of the code goes to them.

This fork adds configurable labels, the Production Start/End Times section, the Month/Year Summary section, and a fix so the battery UI is correctly hidden end-to-end when `_show_battery: false`.

Released under the MIT License — see [LICENSE](LICENSE).
