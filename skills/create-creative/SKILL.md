---
name: create-creative
description: Create, revise, preview, and prepare Nexd rich media ads from a brief using templates and source assets. Use for individual creatives and creative batches in Nexd Campaign Manager.
---

# Create a Nexd creative

Use the connected Nexd MCP tools to turn the user's brief into the requested plan, draft, revision, or published creative. Use the server's current instructions and tool schemas for supported arguments and processing rules.

## Establish the scope

Reuse the brief's campaign choice, format, dimensions, assets, and destination URL. Ask for missing information when it affects the requested result. Template discovery and asset requirements can be answered from the catalog before selecting a campaign.

For campaign work, reuse a campaign already confirmed in the conversation. Otherwise establish whether the user wants a new or existing campaign. For an existing campaign, search with `nexd_list_campaigns` and confirm the selected name and ID. For an authorized new campaign, use `nexd_create_campaign`. A request to plan a new campaign can stay entirely within the dry-run planner.

When work is scoped to an existing campaign or folder, use `nexd_list_campaign_creatives` to select its creatives. The response contains direct children. Include returned subfolders only when the user asks for their contents.

For a supplied creative ID, read its current state with `nexd_get_creative_state` before revising it. Retain the returned IDs for subsequent work.

## Choose the layout

Search `nexd_list_creative_templates` using the requested layout or interaction. Read `nexd_get_template_schema` for a candidate before assigning media. Choose from its actual required slots, accepted media, format, and capabilities. A template can accept both images and video in the same slot.

For additional carousel cards, check `capabilities.custom_feed_assets.supported`. Create each extra slot with `nexd_create_creative_asset_slot` using `type=feed`, then attach its source to the returned slot ID.

## Source and map media

Use the first source that applies to each file.

- **Supplied reference.** Reuse an original asset ID or direct media URL from the brief.
- **Team Library link.** Resolve a library or folder page URL with `nexd_search_team_library_assets`. A page URL is a location to search, so pass the returned original asset IDs onward.
- **Earlier upload.** Find files saved through the Nexd uploader with `nexd_search_user_library_assets`.
- **Local file.** Open the uploader with `nexd_open_upload` and follow the tool's instructions. Ask the user to add the files there and confirm them when processing finishes. When the host cannot display the uploader, offer the library or a direct media URL as the route.

Files attached to the chat stay with the host, so they reach Nexd through the uploader. Use the attachments you can see to prepare that step. Match each one to a slot by its content, filename, and the brief, and settle the template first. Then open the uploader once for the whole set and give the user the list of files to add with the slot each one fills, plus any required slot that has no matching file. After the upload, pair the saved assets with the planned slots by filename and confirm any pairing that is ambiguous.

Map each file to a slot ID from the template schema using the slot's accepted media. Name any required slot that still lacks media before building a complete creative. An explicit request for an empty draft can proceed without media.

Attach saved original asset IDs. The uploader's temporary upload IDs expire with that upload, and a ZIP yields one saved asset per file. Pass references only and keep source media unmodified. Nexd handles cropping, resizing, optimization, and packaging.

When an upload result is unclear, search the personal library before uploading again and reuse whatever was saved. The uploader completes its own imports, and its retry and processing controls resume the existing upload. Report an outcome that stays unclear before starting a new upload.

## Plan or create

Use `nexd_plan_creative_batch` for a media plan or a request to plan several creatives. Include each requested name, size, layout, and available asset mapping. Present unresolved warnings and the proposed work. For a plan-only request, stop after returning the plan. Execute its write steps after the user approves them, reusing approval already given for the same plan.

Create authorized drafts with `nexd_create_creative`. For fixed-size infeed, explicitly pass the user's dimensions, or `size=300x250` if none were supplied. Check the returned dimensions and use `nexd_update_creative_size` to correct a mismatch before adding media. Follow the selected format's sizing rules for fullscreen and responsive creatives.

Attach the selected template with `nexd_attach_creative_template` when the creative does not already have it. Use `nexd_add_creative_asset` with each returned target slot ID and its mapped original source. If the user requested initial placement, pass a nested `transform` using the schema's supported fields and omit `fit_type` and `crop_settings` for that attachment.

Keep IDs from successful steps when a later step fails. Read the current state or processing status before retrying an asset or settings write, then resume from the unfinished step.

## Finish the draft

When crop state is `processing`, wait for the returned retry interval and follow the supplied `nexd_get_creative_asset_crop_status` action. Retry a status read when it is marked retryable. Continue dependent work only after `crop.state=completed`. Check progress through status reads. On a terminal failure, report the failed creative or asset and the returned reason before attempting a repair.

Use the focused tools for revisions. Read `nexd_get_creative_options` before setting a returned option ID. Use `nexd_update_creative_asset_placement` for later framing, and `nexd_update_overlay_animation` with asset IDs from the creative state for animation changes. Attach media before reading `nexd_get_creative_cta` and applying click actions or tracking through their dedicated tools.

Read the completed creative state and get `nexd_get_creative_preview`. Inspect `splash_url` for framing and placement when visual inspection is available. Otherwise return the URL and request visual feedback. Give the user the creative name, ID, actual size, preview URL, and any unresolved work.

## Publish when requested

Run `nexd_validate_publish` for the selected creative IDs and resolve reported issues. A draft or preview request ends with the draft. Call `nexd_publish_creative` with `confirm_publish=true` only after the user explicitly confirms publication of those creatives.

Follow `nexd_get_publish_status` until the job reaches a terminal result. Report the actual outcome for each creative. Once publication succeeds, retrieve requested tags or VAST XML with `nexd_get_creative_publish_artifact`, or use `nexd_get_campaign_tags` with the selected creative IDs for a bundle.
