# Nexd

Create and preview rich media ads in Nexd Campaign Manager from Claude, ChatGPT, or Codex. The plugin connects your existing Nexd account and includes a skill for choosing layouts, mapping source media to template slots, planning sizes, creating drafts, and reviewing previews before publication.

## Get Started

Install the plugin and connect Nexd through the host application's connection settings. Sign in to your Nexd account and review the requested permissions. Your account needs access to the campaigns and actions you request.

Try these prompts.

- Find a mobile carousel template and tell me which assets it needs.
- Plan two carousel ads for a new Summer Launch campaign, at 300x250 and 300x600. Show me the plan first.
- Show me the preview of a draft in my existing Summer Launch campaign.

## Connection and Data

The plugin sends requested tool arguments to Nexd's production MCP endpoint at https://api.nexd.com/v3/mcp. These may include campaign and creative IDs, asset references, creative settings, tracking settings, and report settings. Nexd tools return accessible templates, campaign and creative data, previews, publishing results, and scheduled report information.

Nexd processes the assets you attach and fetches media when you supply a direct media URL. Creating and editing drafts changes your Nexd workspace. Publishing requires explicit confirmation.

## Support

If a tool reports a permissions error, check the connected Nexd account's campaign access. For connection or creative-processing problems, contact [Nexd support](https://support.nexd.com/en/articles/2111540-contacting-nexd-for-support).

## License and Terms

The plugin package uses the [MIT License](LICENSE). Access to the hosted Nexd service is governed by [Nexd's Terms of Service](https://www.nexd.com/terms-conditions/). See the [Privacy Policy](https://www.nexd.com/privacy-policy/) for Nexd's data practices.
