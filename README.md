# IKEA Bilresa Scroll Wheel — Smooth Dimmer / Color / Scene Selector

A Home Assistant **blueprint** for the IKEA **BILRESA scroll wheel** (Matter) that adds
**smooth dimming** and a per-channel **mode selector**: use each wheel as a
**dimmer**, a **color** selector, or a **scene** selector.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FNikolaiL%2Fha-bilresa-scroll-wheel%2Fblob%2Fmain%2Fbilresa_scroll_wheel_smooth.yaml)

## Why

The scroll wheel emits discrete **notch** events (not a hold/release like a push‑dimmer),
so naïve blueprints change brightness in visible **steps**. This blueprint instead glides
the light to an **absolute target** with a **transition** whose length is scaled to how far
you scrolled. Consecutive scrolls blend together, so the bulb's own transition engine
produces a **smooth ramp** rather than jumps. The same absolute‑target + transition
technique is used for color temperature and hue.

## Modes (per channel)

| Mode | What the wheel does |
|------|---------------------|
| **Dimmer (smooth)** | Ramps brightness up/down smoothly. Never dims below a configurable floor (won't turn the light off). Scrolling up while off turns the light on at the floor. |
| **Color temperature (smooth)** | Glides warmer/cooler in Kelvin, clamped to a configurable min/max. |
| **Color / hue (smooth)** | Rotates hue around the color wheel (wraps at 360°) at a configurable saturation. |
| **Scene selector** | Steps through an `input_select` of scene `entity_id`s and activates the selection. |
| **None** | Scroll does nothing on that channel. |

Each channel's **button** supports **click / double‑click / triple‑click / hold** custom actions.

## Requirements

- IKEA **BILRESA scroll wheel** added via **Matter**, exposing `event` entities per channel.
- Home Assistant **2024.10+** (uses `triggers:`/`actions:`/`action:` keys and `color_temp_kelvin`).
- For **scene** mode: an `input_select` helper whose **options are scene `entity_id`s**
  (e.g. `scene.movie`, `scene.relax`).

## Installation

1. Click the **Import Blueprint** badge above (or **Settings → Automations & Scenes →
   Blueprints → Import Blueprint** and paste this file's URL).
2. **Create Automation → from blueprint** → *IKEA Bilresa Scroll Wheel — Smooth*.
3. Pick the **Remote** device. Event entities auto‑detect by numeric suffix
   (Ch1 button `_3` / left `_2` / right `_1`, Ch2 `_6/_5/_4`, Ch3 `_9/_8/_7`). If your
   entity names differ, set them manually in each channel's *Event entities* section.
4. For each channel you use, pick a **Scroll mode** and its **target light(s)** (or the
   scene `input_select`), and optionally configure the button actions.

## Settings

**Dimmer:** brightness step per notch, minimum brightness, **glide time per notch**
(the smoothness knob), max glide time.
**Color:** min/max color temperature, temperature step, hue step, saturation.
**Misc:** scene transition, max queued calls.

Scroll **right = up / warmer / hue+**, scroll **left = down / cooler / hue‑**.

## Notes & limitations

- Evaluation is **relaxed**: one glide per scroll gesture, using the wheel's
  `totalNumberOfPressesCounted` attribute. Because the device reports the count after the
  gesture, the glide begins as you finish scrolling — the motion is smooth, not instant.
- Smooth dimming/color needs bulbs that **support `transition`**. Bulbs that ignore
  transitions will still work but will step.
- **⚠️ Not yet hardware‑tested.** Authored against the documented BILRESA event model; the
  YAML and HA blueprint import are validated, but on‑device behavior should be verified.
  Issues and PRs welcome.

## Credits

Event model and structure inspired by
[aydinseven7/ha-blueprints](https://github.com/aydinseven7/ha-blueprints) (bilresa‑scroll‑wheel)
and the smooth‑ramp idea from
[Kaito‑Mez/HA‑Blueprints](https://github.com/Kaito-Mez/HA-Blueprints) (Bilresa Dual Button).

## License

MIT — see [LICENSE](LICENSE).
