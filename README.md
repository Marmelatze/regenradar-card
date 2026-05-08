[![hacs_badge](https://img.shields.io/badge/HACS-Custom-41BDF5.svg?style=for-the-badge)](https://github.com/hacs/integration)

# Regenradar Card

A Home Assistant Lovelace card that displays an animated precipitation radar map for any location in Germany. Radar data is provided by [Brightsky](https://brightsky.dev/) (Deutsche Wetterdienst).

The card shows a 3-hour radar loop with an OpenStreetMap base layer, a home-location marker, and a play/pause timeline control.

## Requirements

- Home Assistant with the Lovelace UI
- [HACS](https://hacs.xyz/) (for the easiest install path)

## Installation via HACS

1. Open HACS in your Home Assistant sidebar.
2. Go to **Frontend**.
3. Click the three-dot menu in the top-right corner and select **Custom repositories**.
4. Add the URL of this repository and set the category to **Lovelace**.
5. Find **Regenradar Card** in the HACS Frontend list and click **Download**.
6. Reload your browser.

## Manual Installation

1. Download `card.js` from the [latest release](../../releases/latest).
2. Copy it to `<config>/www/regenradar-card/card.js`.
3. Add the resource to your Lovelace configuration:

   ```yaml
   resources:
     - url: /local/regenradar-card/card.js
       type: module
   ```

4. Reload your browser.

## Configuration

Add the card to a dashboard via the UI card picker or directly in YAML:

```yaml
type: custom:regenradar-card
lat: 48.137
lon: 11.576
```

| Option | Type   | Required | Default | Description                        |
|--------|--------|----------|---------|------------------------------------|
| `lat`  | number | yes      | —       | Latitude of the map center         |
| `lon`  | number | yes      | —       | Longitude of the map center        |
| `zoom` | number | no       | `9`     | Initial zoom level                 |

The latitude and longitude can also be configured through the visual card editor in the Home Assistant UI.

## Data Source

Radar data is fetched from the [Brightsky API](https://brightsky.dev/), which redistributes open data from the [Deutsche Wetterdienst (DWD)](https://www.dwd.de/). The card displays a 3-hour window starting from the current time and refreshes automatically every 15 minutes.
