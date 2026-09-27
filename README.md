# State History Card

State History Card is a Home Assistant Lovelace card for state timelines. It is aimed at timelines such as presence, motion, door/window, device tracker, template sensors, lights, switches, and compact numeric sensors where the native history graph is close but you need explicit state colors, numeric color bands, or denser timeline presentation.

It supports:

- Explicit state colors, including raw or translated state aliases such as `on|Home`.
- Optional display label overrides per state.
- Inline labels on long state segments.
- Hover details with state, start, stop, and duration.
- Timeline labels: `on` or `off`.
- Configurable title and legend alignment.
- Numeric color stops with optional scaling, rounding, bucketing, and recorder statistics.
- Clickable left labels for toggle-capable entities, with long press/click for more-info.
- A visual editor for common options, entity rows, global state color/label maps, and global numeric color stops.

Numeric rows are rendered as compact color timelines rather than line graphs. They are best for dense comparison views such as zone temperatures, air quality, power, or other values where color bands are more useful than exact plotted curves.

## Screenshots

![HVAC and weather timelines](img-hvac-weather.png)

<img src="img-light-presence.png" alt="Light and presence timelines" width="520">

## Installation

### HACS

1. Open HACS in Home Assistant.
2. Search for **State History Card**.
3. Install **State History Card**.
4. Refresh the browser.

If HACS does not list the card, add it as a custom repository:

1. Open the three-dot menu and choose **Custom repositories**.
2. Add this repository URL:

```text
https://github.com/stewartoallen/state-history-card
```

3. Select category **Dashboard**.
4. Install **State History Card**.
5. Refresh the browser.

HACS should add the dashboard resource automatically. If you need to add it manually, use:

```text
/hacsfiles/state-history-card/state-history-card.js
```

Resource type:

```text
JavaScript module
```

### Manual install

1. Copy `state-history-card.js` into:

```text
/config/www/community/state-history-card/state-history-card.js
```

2. Add a dashboard resource:

```text
/local/community/state-history-card/state-history-card.js
```

Resource type:

```text
JavaScript module
```

3. Refresh the browser.

## Example

```yaml
type: custom:state-history-card
title: Presence
title_position: left
title_size: 24px
hours_to_show: 24
refresh_interval: 300
legend: on
timestamps: on
labels: ""
state_colors:
  "on|Home": "#22c55e"
  "off|Away": "#64748b"
  unavailable: "#a1a1aa"
  unknown: "#a1a1aa"
state_labels:
  "on": Home
  "off": Away
entities:
  - entity: binary_sensor.kitchen_presence
    name: Kitchen
  - entity: binary_sensor.office_presence
    name: Office
```

## Options

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `entities` | list | required | Entity ID strings or objects with `entity` and optional `name`. |
| `title` | string | none | Card title. Omit it to hide the title. |
| `title_position` | string | `left` | `left`, `center`, or `right`. |
| `title_size` | string/number | theme default | CSS font size such as `20px`, `1.1rem`, or a number treated as pixels. |
| `hours_to_show` | number | `24` | History range in hours. |
| `refresh_interval` | number | `300` | Seconds between history API refreshes. |
| `label_width` | string/number | auto | Left entity label column width. Omit for auto-fit, or set a number of pixels, `120px`, `8rem`, or `24%`. |
| `legend` | string | `on` | `on`, `off`, `left`, `center`, or `right`. `on` and `left` are synonyms. |
| `timestamps` | string | `on` | Timeline labels: `on` or `off`. Midnight marks show a weekday or date instead of `12:00 AM`. |
| `state_colors` | object | `{}` | Global state-to-color map. Keys may match raw state, display label, or `|` separated aliases. |
| `state_labels` | object | `{}` | Global raw-state/display-state-to-label map. Keys may use `|` aliases. |
| `default_color` | string | none | One color for every state **not** named in `state_colors`. Replaces both the built-in `on`/`off`/`home` colors and the hashed per-state fallback, so rows with many distinct values (a temperature, a dimmer level) render as a single color instead of a rainbow. Explicit `state_colors` still win. |
| `entities[].default_color` | string | global | Per-entity override for `default_color`. |
| `color_source` | string | `state` | Global color source for clickable label underlines. Use `light` to use live light attributes when available. |
| `color_stops` | object | none | Global numeric value-to-color stops. Entity-level `color_stops` override this. |
| `null_color` | string | theme background | Color for numeric rows when a value is missing, invalid, or the row has no data. |
| `bucket_minutes` | number | `0` | Global numeric row averaging bucket size in minutes. `0` disables bucketing. Bucket boundaries align to the top of each hour. |
| `recorder` | boolean | `true` | Use Home Assistant recorder statistics for eligible numeric rows when `bucket_minutes` matches a statistics period. Falls back to raw history when statistics are unavailable. |
| `scale` | number | `1` | Global multiplier for numeric color calculation, displayed numeric labels, and numeric segment merging. Applied before `decimals`. Does not affect the raw value shown in hover details. |
| `decimals` | number | none | Global decimal places for numeric color calculation and display labels. Raw values remain visible in hover details. |
| `entities[].mode` | string | `state` | Set to `numeric` to color numeric sensor history from `color_stops`. |
| `entities[].color_stops` | object | none | Numeric value-to-color stops. Overrides global `color_stops`. |
| `entities[].null_color` | string | global/theme background | Per-entity color for missing or invalid numeric values. |
| `entities[].bucket_minutes` | number | global/`0` | Per-entity numeric averaging bucket size in minutes. Bucket boundaries align to the top of each hour. |
| `entities[].recorder` | boolean | global/`true` | Per-entity override for recorder statistics usage. |
| `entities[].scale` | number | global/`1` | Per-entity multiplier for numeric color calculation, displayed numeric labels, and numeric segment merging. Applied before `decimals`. |
| `entities[].decimals` | number | global/none | Per-entity decimal places for numeric color calculation and display labels. |
| `entities[].label_action` | object/string | auto | Set to `toggle` or `{ action: toggle }` to make the left entity label toggle the entity. Set to `off` to disable. |
| `entities[].more_info_entity` | string | row entity | Entity to open for label more-info. Useful when a sensor row should open a related thermostat. |
| `entities[].state_colors` | object | none | Per-entity state color map. Overrides global colors for that entity. |
| `entities[].state_labels` | object | none | Per-entity state label map. Overrides global labels for that entity. |
| `entities[].color_source` | string | global/`state` | Per-entity clickable label underline color source. Use `light` for live light attribute color or `state` for graph state colors. |
| `show_legend` | boolean | `true` | Legacy alias. Set to `false` to hide the legend. |

The card also accepts `colors` as an alias for `state_colors`, `labels` as an alias for `state_labels`, and `factor` as an alias for `scale`. Set `labels: "off"` to hide inline state labels.

## Energy Date Picker Integration (YAML only)

> **Note:** These options are only available through the YAML editor. The visual editor does not support them.

Instead of a fixed `hours_to_show` window, the card can sync its time range with the built-in Home Assistant `energy-date-selection` card. This lets you use the same date picker used by the Energy dashboard and cards like `energy-custom-graph-card`.

### Options

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `use_energy_date_picker` | boolean | `false` | When `true`, the card ignores `hours_to_show` and gets its time range from an `energy-date-selection` card on the same dashboard view. |
| `collection_key` | string | none | Links the card to a specific `energy-date-selection` picker. Use this when you have multiple date pickers on one dashboard. Must match the `collection_key` on the corresponding `energy-date-selection` card. |
| `allow_compare` | boolean | `true` | When the energy date picker's compare toggle is active, a reduced-height compare row appears below each entity showing the comparison period. Set to `false` to disable. |

### Example

Place an `energy-date-selection` card and state-history-card together using the same `collection_key`:

```yaml
type: vertical-stack
cards:
  - type: energy-date-selection
    collection_key: state_history_demo
  - type: custom:state-history-card
    title: Presence History
    use_energy_date_picker: true
    collection_key: state_history_demo
    legend: on
    timestamps: on
    state_colors:
      "on|Home": "#22c55e"
      "off|Away": "#64748b"
    entities:
      - entity: binary_sensor.kitchen_presence
        name: Kitchen
      - entity: binary_sensor.office_presence
        name: Office
```

### How it works

- The `energy-date-selection` card creates a data collection on the HA frontend connection object.
- This card subscribes to that collection and updates its time range whenever the date picker selection changes.
- If the date picker is not yet loaded, the card retries for up to 10 seconds before giving up.
- `refresh_interval` continues to work in energy picker mode — useful for live updates when viewing "today".
- Compare mode adds a smaller row below each entity showing the comparison period side by side with the main period.

## CSS Custom Properties (card_mod)

All visual dimensions, colors, and font sizes are exposed as CSS custom properties. You can override them using `card_mod` or any other method that sets CSS variables on the card element.

| Property | Default | Description |
| --- | --- | --- |
| `--state-history-card-padding` | `16px` | Inner padding of the card content area |
| `--state-history-card-background` | theme card bg | Card background color |
| `--state-history-card-border-radius` | theme radius / `12px` | Card border radius |
| `--state-history-title-font-size` | `24px` | Title font size (overridden by `title_size` option) |
| `--state-history-title-font-weight` | `normal` | Title font weight |
| `--state-history-title-color` | theme primary text | Title text color |
| `--state-history-title-padding` | `16px 16px 0` | Padding around the title |
| `--state-history-label-font-size` | `13px` | Entity name label font size |
| `--state-history-label-font-weight` | `normal` | Entity name label font weight |
| `--state-history-label-color` | theme primary text | Entity name label color |
| `--state-history-label-line-height` | `18px` | Entity name label line height |
| `--state-history-row-height` | `18px` | Height of each timeline track |
| `--state-history-row-gap` | `10px` | Vertical gap between entity rows |
| `--state-history-track-border-radius` | `4px` | Border radius of timeline tracks |
| `--state-history-track-background` | theme secondary bg | Track background (behind segments) |
| `--state-history-track-grid-color` | theme divider | Color of the 25% grid lines |
| `--state-history-future-color` | `rgba(0,0,0,0.15)` | Overlay color for the future (after "now") portion of today's track. Set to `transparent` to disable |
| `--state-history-segment-font-size` | `11px` | Font size of labels inside timeline segments |
| `--state-history-segment-font-weight` | `500` | Font weight of segment labels |
| `--state-history-compare-row-height` | `row-height × 0.6` | Height of compare period tracks |
| `--state-history-compare-row-opacity` | `0.5` | Opacity of compare period rows |
| `--state-history-compare-row-margin-top` | `-6px` | Gap between main and compare rows |
| `--state-history-compare-label-font-size` | `10px` | Font size of the compare period label |
| `--state-history-axis-font-size` | `11px` | Font size of time axis labels |
| `--state-history-axis-color` | theme secondary text | Time axis text color |
| `--state-history-axis-tick-color` | theme divider | Color of axis tick marks |
| `--state-history-legend-font-size` | `12px` | Font size of legend items |
| `--state-history-legend-color` | theme secondary text | Legend text color |
| `--state-history-legend-gap` | `8px 14px` | Gap between legend items (row column) |
| `--state-history-legend-margin-top` | `14px` | Space above the legend |
| `--state-history-swatch-size` | `10px` | Size of legend color swatches |
| `--state-history-tooltip-font-size` | `12px` | Tooltip font size |
| `--state-history-tooltip-border-radius` | `6px` | Tooltip border radius |
| `--state-history-tooltip-padding` | `8px 10px` | Tooltip inner padding |

### card_mod example

```yaml
type: custom:state-history-card
card_mod:
  style: |
    ha-card {
      --state-history-row-height: 24px;
      --state-history-row-gap: 14px;
      --state-history-label-font-size: 14px;
      --state-history-legend-font-size: 13px;
      --state-history-track-border-radius: 6px;
      --state-history-compare-row-opacity: 0.4;
    }
entities:
  - entity: binary_sensor.kitchen_presence
```

## State Matching

Home Assistant history stores raw values such as `on`, `off`, `home`, and `not_home`, while the frontend often displays translated labels such as `Home` and `Away`.

State color and label keys can match either form:

```yaml
state_colors:
  "on|Home": "#22c55e"
  "off|Away": "#64748b"
state_labels:
  "on|Home": Present
  "off|Away": Clear
```

To hide labels inside the colored state segments while keeping tooltip and legend labels:

```yaml
labels: "off"
```

Per-entity overrides use the same syntax:

```yaml
type: custom:state-history-card
entities:
  - entity: sensor.ac_state
    state_colors:
      idle: "#64748b"
      cooling: "#0284c7"
      heating: "#dc2626"
```

## Label Actions

For `light`, `switch`, `fan`, and `input_boolean` entities, the left entity label calls `homeassistant.toggle` on short click/touch. Long click/touch opens Home Assistant more-info.

```yaml
type: custom:state-history-card
entities:
  - entity: light.office
    name: Office
```

Use `label_action: off` to disable the short-click toggle for an entity. Use `label_action: toggle` to enable it for another domain.

For entities without a short-click action, clicking the label opens more-info. Set `more_info_entity` to open a related control entity instead of the displayed history entity:

```yaml
entities:
  - entity: sensor.t6_pro_z_wave_programmable_thermostat_with_smartstart_air_temperature
    name: thermostat
    more_info_entity: climate.t6_pro_z_wave_programmable_thermostat_with_smartstart
```

The history bar itself remains dedicated to hover/touch history details.

## Numeric Color Stops

Numeric sensors can render as interpolated color bands:

```yaml
type: custom:state-history-card
color_stops:
  60: "#2563eb"
  68: "#22c55e"
  76: "#facc15"
  82: "#dc2626"
null_color: "#3f3f46"
decimals: 1
bucket_minutes: 5
entities:
  - entity: sensor.office_temperature
    name: Office temp
    mode: numeric
  - entity: sensor.living_room_temperature
    name: Living room temp
    mode: numeric
    color_stops:
      62: "#2563eb"
      72: "#22c55e"
      84: "#dc2626"
    decimals: 0
    recorder: false
```

Numeric rows are omitted from the discrete state legend.

When global `color_stops` are configured, `sensor`, `number`, and `input_number` entities that look numeric use the stops automatically. Use `mode: state` to force a sensor to use discrete state colors. Use `mode: numeric` to force numeric handling for another entity.

`scale` is applied before `decimals`. Scaled and rounded values are used for color selection, visible numeric labels, and numeric segment merging. Hover details keep a raw value; bucketed raw values are formatted with `decimals` when configured, or a compact fallback precision.

`bucket_minutes` reduces dense numeric history into duration-weighted averages. Buckets align to the top of each hour, so a 5-minute bucket always starts at `HH:00`, `HH:05`, `HH:10`, and so on. `bucket_minutes: 0` disables bucketing.

With `bucket_minutes` configured, the card prefers Home Assistant recorder statistics for eligible numeric sensors by default. `bucket_minutes: 5`, `15`, and `30` use 5-minute statistics when available; `bucket_minutes: 60` uses hourly statistics. The card falls back to raw history when statistics are unavailable. Set `recorder: false` to force raw history.

## Light Color Source

Light rows can use their current reported color for clickable label underlines:

```yaml
type: custom:state-history-card
color_source: state
entities:
  - entity: light.corner
    color_source: light
  - entity: sensor.office_temperature
    mode: numeric
```

Set `color_source: light` globally if most label underlines should use live light colors, then override non-light or discrete rows with `color_source: state`. The history graph itself continues to use `state_colors` or built-in state defaults.

## Visual Editor

The visual editor supports the common card options, entity rows, global state color/label maps, and global numeric color stops. Advanced per-entity `state_colors`, `state_labels`, `color_stops`, `bucket_minutes`, `recorder`, `scale`, `decimals`, `label_action`, and `more_info_entity` remain available through the raw YAML editor.

## Development

Run the syntax check:

```bash
npm run check
```

For local Home Assistant testing, update the dashboard resource URL with a cache-busting query string after each code change:

```text
/local/community/state-history-card/state-history-card.js?v=1
```

Then hard-refresh the Home Assistant browser tab.

For crude local timing diagnostics, set `ENABLE_BENCHMARK_LOGS` to `true` at the top of `state-history-card.js`. The card logs load and render timing with a `[state-history-card]` prefix.
