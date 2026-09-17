---
name: figurelabs-canvas-edit
description: Edit existing FigureLabs figures by calling the matching MCP edit operation directly, then open or refresh the project before delivery.
---

# Edit the requested figure

Use only this plugin's `figurelabs` connection. Preserve the originating tool, project, workspace, `source_id` and `version_id` context.

For every user modification request, call the matching FigureLabs MCP tool with `operation=edit` directly. Before submitting the edit, if a FigureLabs project page is already open in a browser and the canvas is observable, check which image is currently selected on the canvas. Unless the user explicitly names another target, treat the currently selected canvas image as the image to modify. If no image is selected, default to the last generated image in the current project. Do not choose a manual page operation based on this inspection.

Pass the user's instruction, target identifiers, requested ratio or structure, attachments, and a stable `idempotency_key`. If the selected or user-supplied target image exposes usable `source_id`/`version_id`, use those IDs. If it exposes a FigureLabs file id or server-accessible HTTPS image URL instead, pass that image according to the tool schema and identify it in `instruction` as the image to modify. Do not invent source/version IDs or image URLs.

Continue with the original job polling flow after submitting the edit. After the edit completes, apply [figurelabs-browser-handoff](../figurelabs-browser-handoff/SKILL.md): open or refresh the FigureLabs project before delivery while preserving any required context. On success, direct the user to the opened page and provide the project link without adding a chat image by default. For an explicit chat-image request or failed browser delivery, follow the preview-first and eligible local-file fallback there. Do not embed image_url/download_url directly in chat; report the actual browser/display outcome.
