---
name: animate-overlays
description: Apply default motion when adding a visible CTA button to a Nexd creative. Suggest, plan, or edit fades, slides, scale pulses, rotation, and timing for overlays.
---

# Animate creative overlays

For a showcase or plan, describe what the viewer sees and when. For example, the logo fades in over the first half-second, then the CTA slides up over the next 0.4 seconds. A general example needs no creative ID. Stop at the proposal when the user asks for ideas or a plan only.

## Choose the motion

Each example is one entry in the tool's `animations` list. Set `delay` to the start time measured from the beginning of the timeline. Both `delay` and `duration` use milliseconds.

| Viewer sees | Animation entry |
| --- | --- |
| Logo fades in immediately | `{"type":"fade-in","delay":0,"duration":500}` |
| CTA slides up at the half-second mark and slows into place | `{"type":"move-in","side":"bottom","delay":500,"duration":400,"ease":"outQuad"}` |
| Headline grows from 90% to full size | `{"type":"scale","from":0.9,"to":1,"delay":0,"duration":300}` |
| Badge makes a half-turn in place | `{"type":"rotate","axis":"z","from":0,"to":3.14159,"delay":0,"duration":600}` |

Use `fade-out` or `move-out` for exits. Rotation uses radians.

For the pin that opens hotspot media, use the **Pin animation** control described in the [hotspot workflow](../manage-hotspots/SKILL.md).

## Keep a CTA visible after its entrance

Give a newly added CTA a gentle recurring scale pulse even when the brief mentions motion only for another overlay. Grow to about 105% and return to the original size, with a rest between pulses. Reuse a matching pulse already present on that button. On a one-shot timeline, fade the button in once and schedule the pulses through the spot. On an existing loop, start the CTA visible and fit the pulse pairs inside the shared cycle so other overlays keep their timing. Follow explicit directions for static, entrance-only, or custom motion.

For example, on a six-second timeline that plays once, use these entries on the CTA. Each delay is measured from the start of the timeline.

| Effect | Delay (ms) | Duration (ms) | Result |
| --- | ---: | ---: | --- |
| `fade-in` | 0 | 500 | Button becomes fully visible |
| `scale` | 1500 | 300 | Grow from 1 to 1.05 |
| `scale` | 1800 | 300 | Return from 1.05 to 1 |
| `scale` | 4500 | 300 | Grow from 1 to 1.05 |
| `scale` | 4800 | 300 | Return from 1.05 to 1 |

`timeline.iterations=1` plays the entrance once. Schedule additional pulse pairs for a longer spot. With `timeline.iterations=0`, the whole timeline loops, including the entrance and other overlays. For a continuously visible CTA on a looping creative, start it visible and repeat just the grow-and-return motion on that asset.

## Fit every effect inside the shared timeline

For an existing creative, read `nexd_get_creative_animations` before changing timing. Take the largest `delay + duration` across all enabled overlays, including the proposed edits. Keep the global duration at least that long. For a loop, leave a short hold after the final motion so it can settle before playback restarts. The example above finishes at 5100 ms and holds for 900 ms on a 6000 ms timeline. An effect on another overlay ending later sets the minimum for the whole creative.

Keep an existing longer duration when it fits. If the brief fixes the length or playback follows a video, fit the effects inside that window. Extend a flexible timeline when the requested motion needs more time. Check the playable preview through the last effect and, for a loop, through the restart.

## Apply an edit

1. Read the selected creative with `nexd_get_creative_state`. Match the requested overlay to its attached `assets[].asset_id`. If the target is missing or ambiguous, identify the missing overlay or ask which of the matching assets to animate.
2. Use `nexd_update_overlay_animation` for one asset at a time. Choose `append` to add an effect. To retime one effect, read `nexd_get_creative_animations`, change that entry, and send the target's full updated list with `replace`. Use `disable` to turn off the overlay's motion while keeping its entries, or `remove` to delete its animation entry.
3. Check the returned animation and timeline, then get `nexd_get_creative_preview`. For a new CTA, verify the grow-and-return pair on the button's own asset ID and report the timing to watch at the playable `preview_url`. If an append result is unclear, read the saved timeline before retrying so the effect is added only once.
