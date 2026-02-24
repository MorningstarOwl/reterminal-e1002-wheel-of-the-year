# Wheel of the Year — Wicca Calendar Display

ESPHome configuration for the reTerminal E1002 displaying a Wicca calendar with lunar phases, the next Sabbat, sun sign, current season, planetary positions, and a full Sabbat countdown grid — all sourced from the [Wheel of the Year](https://github.com/MorningstarOwl/ha-wheel-of-the-year) Home Assistant integration.

## Display Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│  HEADER  (y 0–52)  Black bg / White+Yellow text                        │
│  "WHEEL OF THE YEAR"    date    season badge    battery%               │
├──────────────────────────────┬──────────────────────────────────────────┤
│  MOON PHASE  (x 0–310)      │  NEXT SABBAT  (x 320–520)  │ SEASON+   │
│  Drawn moon disc             │  Name + countdown          │ SUN SIGN  │
│  Phase name + illumination % │  Alt name + date           │ (x 530–   │
│  Magickal associations       │  Brief description         │  800)     │
│  (y 56–262)                  │  (y 56–262)                │           │
├──────────────────────────────┴────────────────────────────┴───────────┤
│  PLANETARY POSITIONS  (y 256–310)                                     │
│  Sun · Moon · Mercury · Venus · Mars · Jupiter · Saturn               │
├───────────────────────────────────────────────────────────────────────┤
│  SABBAT COUNTDOWNS  (y 316–478)  — 8 sabbats, 2 rows × 4 columns    │
│  Row 1:  Yule  │  Imbolc  │  Ostara  │  Beltane                      │
│  Row 2:  Litha │ Lughnasadh │  Mabon  │  Samhain                     │
└───────────────────────────────────────────────────────────────────────┘
```

## Color Legend

| Color | Usage |
|-------|-------|
| Black | Header background, body text, rules |
| Yellow | Title text, solar sabbats, sun/Jupiter planets, accent lines |
| White | Header date/battery |
| Blue | "LUNAR PHASE" label, Moon planet, Winter season, countdown days |
| Red | "NEXT SABBAT" label, fire/cross-quarter sabbats (Imbolc, Beltane, Lughnasadh, Samhain), Mars, Autumn season |
| Green | Sun sign element, "SEASON" label, Venus planet, Spring season |

## Moon Disc

The moon is drawn as a filled circle with a brightness approximation based on illumination percentage from the integration. Waxing phases light from the right; waning phases light from the left.

## Sabbat Grid

The upcoming sabbat is highlighted with a yellow filled cell. Solar sabbats (solstices/equinoxes) are labeled in yellow; cross-quarter sabbats are labeled in red. Countdown shows days remaining.

## Prerequisites

### Home Assistant Integration

Install the **Wheel of the Year** integration via HACS:

> [https://github.com/MorningstarOwl/ha-wheel-of-the-year](https://github.com/MorningstarOwl/ha-wheel-of-the-year)

This provides all sensor entities used by this configuration:

| Entity | Used for |
|--------|----------|
| `sensor.wheel_of_the_year_moon_phase` | Phase name, illumination, magick, emoji |
| `sensor.wheel_of_the_year_sun_sign` | Sign name, symbol, element |
| `sensor.wheel_of_the_year_current_season` | Season name, emoji, short description |
| `sensor.wheel_of_the_year_next_sabbat` | Next sabbat name, alt name, date, description |
| `sensor.wheel_of_the_year_sun_position` | Sun planetary position |
| `sensor.wheel_of_the_year_moon_position` | Moon planetary position |
| `sensor.wheel_of_the_year_mercury_position` | Mercury planetary position |
| `sensor.wheel_of_the_year_venus_position` | Venus planetary position |
| `sensor.wheel_of_the_year_mars_position` | Mars planetary position |
| `sensor.wheel_of_the_year_jupiter_position` | Jupiter planetary position |
| `sensor.wheel_of_the_year_saturn_position` | Saturn planetary position |
| `sensor.wheel_of_the_year_solar_cycle` | Solar cycle |
| `sensor.wheel_of_the_year_yule` … `_samhain` | Sabbat countdown values (8 total) |

## Secrets Required

The following keys must be present in your `secrets.yaml`:

```yaml
wifi_ssid: "your_network"
wifi_password: "your_password"
ap_password: "your_fallback_ap_password"
api_key: "your_base64_api_key="
ota_password: "your_ota_password"
```

## Flashing

```bash
esphome run wheel-of-the-year/e1002-wheel-of-the-year.yaml
```

## Manual Refresh

The green button on top of the device (GPIO3) triggers an immediate display refresh when pressed. This is useful for forcing an update outside of the hourly cycle without waiting for the next SHT40 reading.

## Notes

- The display refreshes every **hour** (driven by the SHT40 `on_value` callback at `update_interval: 3600s`). Color ePaper takes ~30 seconds to complete a full refresh. The SHT40 readings are marked `internal: true` since they are only used as a refresh trigger, not exposed to Home Assistant.
- Requires **ESPHome ≥ 2025.11.0** for the `Seeed-reTerminal-E1002` display model.
- The Arduino framework is mandatory for 8 MB PSRAM support.
- Fonts used: **Cinzel** (thematic serif for headers) and **Inter** (clean sans-serif for body). Both loaded via `gfonts://`.
