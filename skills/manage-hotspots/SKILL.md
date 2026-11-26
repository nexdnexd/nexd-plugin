---
name: manage-hotspots
description: Plan, add, or edit Nexd hotspots that reveal media on tap. Use for global or card-specific pins, artwork, placement, pin animation and visibility, video pausing, closing behaviour, and backdrop settings.
---

# Manage hotspots

A hotspot has a pin the viewer taps, media that opens, and a way to close it. Establish where the pin should appear and what it should reveal. A pin on one product card belongs to that card. If the requested tap goes straight to a website, use a URL click action on the intended asset. For a plan-only request, describe the interaction and stop before making edits.

## Add or update hotspot media

For an edit to a selected draft, read `nexd_get_creative_state` first. Keep hotspot work in chat. Give Campaign Manager instructions only when the user explicitly asks for manual steps.

- **Global hotspot.** For a new hotspot across the creative, call `nexd_create_creative_asset_slot` once with `creative_id`, a descriptive `name`, and `type=hotspot`. Keep the returned `result.asset.asset_id`. In a batch dry run, use `custom_slot_type=hotspot` on the revealed media. If slot creation has an uncertain outcome, check the draft for the named slot before retrying.
- **Hotspot on one card or asset.** Match the requested card to an existing hotspot main slot. The compact asset list omits the parent relationship. Use a slot the user has identified on that card by name or ID, or a mapping already confirmed for this draft. If candidate slots are ambiguous, ask which belongs to the card, using the names from the draft. Slot order and pin position cannot establish ownership.
- **Existing card hotspot.** Fill an empty slot on that card or replace the media in the specific hotspot the user asked to edit. Use the hotspot main slot's ID as `target_asset_id`. Targeting the card artwork would replace the card image. In a batch plan for an existing slot, omit `custom_slot_type`.
- **Missing card hotspot.** If the card has no suitable slot, explain that MCP can update an existing card hotspot but currently cannot create one tied to a single card. Leave that pin pending. A global pin would appear on the other cards too. Continue any independently requested work within the user's scope.

Attach the requested image or video with `nexd_add_creative_asset`, using the main slot ID as `target_asset_id` and an original asset ID or direct media URL as the source. Follow any returned crop-status action to completion. Nexd supplies a default pin and close icon. The pin opens the revealed media automatically. If the revealed product image should also link out, read `nexd_get_creative_cta` and apply the URL to the main media's own CTA entry when available. Preserve the pin's open action.

## Edit hotspot settings

For a plan or explanation, describe the requested behaviour without writing. For a draft edit, match the named hotspot to its **main media slot ID**. Its entry in the creative's `hotspots` settings controls the interaction.

| Requested behaviour | Field in that hotspot's entry |
| --- | --- |
| Fade, pulse, or keep the pin static | Set `gesture_animation.type` to `"fade"` (default), `"pulse"`, or `""` (None). Pin motion uses this field independently of the overlay timeline. |
| Keep the pin visible or hide it after opening | Set `gesture_keep_visible` to `true` or `false`. |
| Pause other videos while open, or let them play | Set `pause_other_media` to `true` or `false`. |
| Show a close icon, or close by tapping another pin | Set `closing_behaviour` to `"default"` or `"switch"`. Switch mode needs another reachable pin. |
| Dim the area behind the open media | With **Show close icon**, set `settings.background_color` to a hex colour and `settings.background_alpha` to opacity from 0 to 1 (40% is `0.4`). Switch mode disables the backdrop and close icon. If the brief asks for both switching and dimming, ask which interaction to keep. |

1. Read `nexd_get_creative_options`. Use `nexd_update_creative_option` if it returns an option for that exact hotspot and control.
2. Otherwise, `nexd_update_creative_settings` takes `creative_id`, `key="hotspots"`, and the **complete current hotspot map** as `value`, with the requested fields edited in place. Use a current map for this draft, for example one supplied by the user or returned by a just-completed settings edit. Preserve every other entry, the target's `gesture_id` and `close_id`, and its other nested fields. Group edits to the same map in one call.
3. When initializing the first hotspot you just created and populated on a fresh draft with no prior hotspots, the whole map has one new entry. Key it by the returned main ID and use its matching pin and close IDs. The new-slot defaults are Fade, keep visible on, pause other media on, Show close icon, and a 70% black backdrop. Apply the requested changes to those defaults.
4. For existing hotspots, the compact creative state and option list do not expose the full map. Slot names or IDs alone cannot recover saved settings. If the current map is missing or stale, explain the intended change in chat and ask for the current hotspot settings. Leave the edit pending. After a successful write, check the returned settings against the intended edit and use the playable preview to check the interaction.

## Change pin artwork or placement

For custom pin or close artwork, use the matching component ID. In a returned `custom_hs_…_main` family, the pin and close IDs have the same stem and end in `_pin` and `_close`. Attach the requested image to that component. Use `nexd_update_creative_asset_placement` on an attached pin to move where the viewer taps. Moving the main asset changes the revealed media's placement.

When the user asks for Campaign Manager steps, select the hotspot media and expand **Hotspot settings** to edit the controls above. **Pin gesture** and **Close icon** also offer stock artwork. To add a card hotspot, open the populated card's **Hotspots → Add hotspot media**. **Delete additional slot** removes the hotspot group. Clearing its media leaves the slot in the creative.

## Check the interaction

Get `nexd_get_creative_preview` and use its playable `preview_url`. Tap each pin, confirm the intended media opens, then close it or switch to the next pin. Where a global pin and a card-specific pin appear together, check that both are reachable. Change cards and confirm the card-specific pin disappears. If playback is unavailable, share the preview and identify the interaction that still needs checking.
