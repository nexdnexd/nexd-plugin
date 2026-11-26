---
name: traffic-media-plan
description: Traffic or audit Nexd ad placements from a media plan and tracker sheet. Use for matching agency placement IDs to creatives, building placement variants, checking impression and click trackers, or returning the completed plan.
---

# Traffic a media plan

## Match placements and trackers

Read the placements and tracker rows supplied by the user. Join them by agency placement ID, keeping each ID as text so leading zeros survive. A placement can have several tracker rows. If the sheets lack a shared ID, use another key the user identifies or ask about ambiguous matches before changing those placements.

For each placement, keep its creative name, format, size, assets, destination, required tracker events and any supplied Nexd creative ID. Preserve tracker URLs and macros exactly. A landing URL is a click destination. A click tracker is a separate measurement URL.

Identify gaps against the plan's requirements. Report the placement ID and whether the whole tracker row is missing, a required event lacks a URL, or the mapping is ambiguous. A blank cell alone never authorizes clearing a saved tracker.

For a plan-only request, show tracker coverage from the supplied sheets. If the user asks to plan new creatives, use `nexd_plan_creative_batch` in groups of up to 25 creatives and include available tracker mappings. Show which placements need a fresh build and which can copy a shared creative, then stop at the dry run. For an audit of saved tracking, read the selected creatives and report discrepancies.

## Build placement variants

For campaign work, reuse the confirmed campaign or establish whether the user wants a new or existing one. For an existing campaign, use `nexd_list_campaigns` and confirm the name and ID. Select creatives in the requested campaign or folder with `nexd_list_campaign_creatives`. For an authorized new build, create the campaign once and reuse its ID.

Reuse creatives already mapped to the plan. When new placements share a layout, size and artwork, copy a matching creative from the selected source. If there is no source creative, build the first placement once and use it for the remaining copies. Use the [creative workflow](../create-creative/SKILL.md) for layout, asset slots and crop processing when building from scratch.

Use `nexd_duplicate_creative` with one source ID and up to 20 requested names per call. Record each returned creative ID against its placement as soon as the mapping is clear. Follow each processing copy's retry interval and `nexd_get_creative_duplication_status` action until completion. Copies start in the source campaign. If the destination differs, move the completed copies with `nexd_move_creatives` and check the per-ID results. If a write's outcome is unclear, check the creative IDs and their locations before retrying.

## Apply and verify tracking

Read `nexd_get_creative_cta` for each mapped creative, including new copies. Compare each required event and URL at its intended target, either global or a specific asset. Flag unexpected trackers inherited from another placement.

For authorized edits, use `nexd_update_creative_tracking` at the target specified by the sheet or brief. Use `impression` for impression trackers and `ctr` for click trackers. When adding tracking, append only missing entries. Use `replace` only when the user intends the supplied list to replace that target's trackers. Read back changed targets before reporting a match or retrying an uncertain write.

Report how many placements match and list exceptions by placement ID. A passing `nexd_validate_publish` result covers export eligibility. Tracker coverage comes from comparing saved events, URLs and targets with the plan.

## Return the plan

Keep the source row order. When the user supplied a workbook and the host can edit it, return an updated copy with the Nexd creative ID, tracker status and available preview link for each placement. Otherwise return the same fields in a table. Mark planned placements as planned and distinguish source gaps from discrepancies in saved tracking.

For publication, surface unresolved exceptions and validate the selected creatives. Publish only the IDs the user explicitly approves. Follow the publish job to completion and include requested tags for the placements that published successfully.
