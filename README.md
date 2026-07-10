# IKEA Bilresa Scroll Wheel — Smooth Dimmer / Color / Scene

A Home Assistant **blueprint** for the IKEA **BILRESA scroll wheel** (Matter) that adds
**smooth dimming** plus a **mode selector**: use a wheel channel as a **dimmer**, a
**color** selector (color temperature or hue), or a **scene** selector — controlling the
**light or light group you choose**.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FNikolaiL%2Fha-bilresa-scroll-wheel%2Fblob%2Fmain%2Fbilresa_scroll_wheel_smooth.yaml)

## Model: one automation = one channel → one light/group

The wheel has **3 channels**, each with two scroll directions and a push button. You create
**one automation per channel** and point it at the light (or light group) it should control.

Event-entity mapping (Matter):

| Channel | Scroll A (`up`) | Scroll B (`down`) | Button |
|--------:|-----------------|-------------------|--------|
| 1 | `..._button_1` | `..._button_2` | `..._button_3` |
| 2 | `..._button_4` | `..._button_5` | `..._button_6` |
| 3 | `..._button_7` | `..._button_8` | `..._button_9` |

(The scroll entities are the ones exposing a `totalNumberOfPressesCounted` attribute; the
button exposes `long_press`/`long_release`.) If a channel's direction feels reversed, just
swap the two scroll entities.

## Why "smooth"

The wheel emits discrete **notch** events, not a hold/release, so naïve blueprints step
brightness in visible jumps. This one glides the light to an **absolute target** with a
**transition** scaled to how far you scrolled (*Glide time per notch*). Consecutive scrolls
blend, so the bulb's own transition engine produces a continuous ramp. The same
absolute-target + transition technique is used for color temperature and hue.

## Modes

| Mode | What the wheel does |
|------|---------------------|
| **Dimmer (smooth)** | Ramps brightness up/down; never below the floor (won't switch off). Scrolling up while off turns the light on at the floor. |
| **Color temperature (smooth)** | Glides warmer/cooler (Kelvin), clamped to min/max. |
| **Color / hue (smooth)** | Rotates hue around the wheel (wraps at 360°). |
| **Scene selector** | Steps an `input_select` of scene `entity_id`s and activates it. |

The button supports **click / double / triple / hold** custom actions.

## Setup

1. Import via the badge above (or **Settings → Automations & Scenes → Blueprints → Import
   Blueprint** and paste this file's URL).
2. **Create Automation → from blueprint** → *IKEA Bilresa Scroll Wheel — Smooth*.
3. Set:
   - **Light(s) / light group to control** — one group entity or several lights.
   - **Scroll UP / DOWN** event entities and the **Button** entity for the channel
     (see the table above).
   - **Scroll mode** and, for scene mode, the **scene `input_select`**.
4. Repeat for other channels if desired (up to 3 automations).

## Settings

**Dimmer:** brightness step per notch, minimum brightness, **glide time per notch**
(smoothness), max glide. **Color:** min/max color temperature, temperature step, hue step,
saturation. **Misc:** scene transition, max queued calls.

## Requirements

- IKEA **BILRESA scroll wheel** added via **Matter** (exposes `event.*_button_1..9`).
- Home Assistant **2024.10+**.
- Smooth transitions require bulbs that support `transition` (otherwise they step).
- Scene mode needs an `input_select` whose options are scene `entity_id`s.

## Credits

Event model from [aydinseven7/ha-blueprints](https://github.com/aydinseven7/ha-blueprints);
smooth-ramp idea from [Kaito-Mez/HA-Blueprints](https://github.com/Kaito-Mez/HA-Blueprints).

## License

MIT — see [LICENSE](LICENSE).
