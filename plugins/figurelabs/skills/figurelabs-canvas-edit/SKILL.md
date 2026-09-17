---
name: figurelabs-canvas-edit
description: Edit existing FigureLabs figures by calling the matching MCP edit operation directly, then open or refresh the project before delivery.
---

# Edit the requested figure

Use only this plugin's `figurelabs` connection. Preserve the originating tool, project, workspace, `source_id` and `version_id` context.

For every user modification request, call the matching FigureLabs MCP tool with `operation=edit` directly. Do not inspect the browser canvas, selected image, page controls, or existing canvas capabilities before editing. Do not choose a manual page operation based on a canvas inspection.

Pass the user's instruction, target identifiers, requested ratio or structure, attachments, and a stable `idempotency_key`. If the user supplied a target image or file, identify it in `instruction` and pass it according to the tool schema. Do not invent source/version IDs.

Continue with the original job polling flow after submitting the edit. After the edit completes, apply [figurelabs-browser-handoff](../figurelabs-browser-handoff/SKILL.md): open or refresh the FigureLabs project before delivery while preserving any required context. On success, direct the user to the opened page and provide the project link without adding a chat image by default. For an explicit chat-image request or failed browser delivery, follow the preview-first and eligible local-file fallback there. Do not embed image_url/download_url directly in chat; report the actual browser/display outcome.
