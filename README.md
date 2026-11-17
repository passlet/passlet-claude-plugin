# Passlet

Create Apple Wallet and Google Wallet passes through conversation. Passlet is a digital wallet pass creator for businesses, clubs and event organizers: design branded membership cards, loyalty cards, event tickets and season passes from a brief, logo or existing brand assets.

Build reusable pass templates with your colors, artwork, text and supported QR codes or barcodes. Personalize each pass with fields such as a member name, membership tier, points balance, ticket number or expiry date. Inspect Apple Wallet and Google Wallet previews, refine the design and check layout limitations before publishing. Previews are approximations; the final appearance depends on the wallet and device.

Publish templates, issue personalized passes individually or in batches, and retrieve Add to Apple Wallet and Add to Google Wallet links to share with holders. Find existing passes, update their details, move them to a newer published template version, void passes or revoke their add-to-wallet links. Organize templates and passes into projects for different events, clients or membership programs.

Connect your Passlet account and choose a workspace to create and manage passes with the permissions you approve. Pass issuance is subject to your workspace's plan and limits.

## Example prompts

- Create an Apple Wallet and Google Wallet membership card using my logo, with a member name, tier and expiry date.
- Design a digital loyalty card for Apple Wallet and Google Wallet with a customer name and points balance.
- Create event ticket passes for Apple Wallet and Google Wallet from my event artwork and attendee list.

## Skills

- **design-from-brand**: Create or refine Apple Wallet and Google Wallet pass templates in Passlet for membership cards, loyalty cards, event tickets and season passes. Turn a brief, logo or brand assets into a personalized pass design, inspect wallet previews and improve the draft. Use for pass creation, layout, branding, imagery and field hierarchy; not for unrelated brand design or issuing passes to holders.
- **manage-passes**: Publish Passlet templates and issue personalized Apple Wallet and Google Wallet passes, including digital membership cards, loyalty cards and event tickets. Find, read, update, upgrade or void issued passes and retrieve or revoke add-to-wallet links. Use for individual or batch issuance and changes to holders' passes; not for designing a template's look or organizing projects.
- **manage-projects**: Organize Apple Wallet and Google Wallet pass templates and their issued passes into Passlet projects for events, clients or membership programs. List, create, rename, recolor or delete projects, move templates between projects and report template and pass counts. Use for grouping, sorting or summarizing workspace content; not for designing templates or changing individual passes.

## Install in Claude Code

Add this repository as a plugin marketplace, then install the plugin:

```text
/plugin marketplace add passlet/passlet-claude-plugin
/plugin install passlet@passlet
```

## Connection and data

You need a Passlet account (sign up at https://passlet.io). This plugin runs no local code. Its skills use the Passlet MCP server at https://mcp.passlet.io/mcp over HTTPS. On first use, sign in to Passlet, choose a workspace and approve the requested permissions (`openid`, `profile`, `email`, `offline_access`, `templates:read`, `templates:write`, `passes:read`, `passes:write`). Claude then reads and changes that workspace's templates, passes and projects through the server, limited by your role and the permissions you approved. Text, images and pass details you give Claude for a design or pass are sent to Passlet to create or update them in that workspace. Revoke access at any time under **Settings → Connected apps** in the Passlet console.

- Website: https://passlet.io
- Documentation: https://docs.passlet.io
- Support: https://www.passlet.io/contact
- Privacy policy: https://www.passlet.io/privacy-policy
- Terms of service: https://www.passlet.io/terms-conditions
