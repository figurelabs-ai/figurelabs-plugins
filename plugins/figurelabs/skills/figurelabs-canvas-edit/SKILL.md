---
name: figurelabs-canvas-edit
description: Edit existing FigureLabs figures by calling the matching MCP edit operation directly, then open or refresh the project before delivery.
---

# Edit the requested figure

Use only this plugin's `figurelabs` connection. Preserve the originating tool, project, workspace, `source_id` and `version_id` context.

For every user modification request, call the matching FigureLabs MCP tool with `operation=edit` directly. Before deciding edit arguments, reset the target assumption completely: ignore any earlier inclination from chat history, the latest generated result, prior planned `source_id`/`version_id`, or the user's wording unless the user explicitly names a concrete target. Do not use browser inspection to confirm a preselected target; use it to decide the target from scratch.

Before submitting the edit, if a FigureLabs project page is already open in a browser, determine the selected canvas image using only these two methods, in order:

1. DOM/accessibility contract. First read the browser-visible DOM contract; do not rely on `window.figurelabsCanvas` as the automation path. Prefer `#figurelabs-selected-image-state`. If it exists and has `data-selected="true"`, treat it as authoritative and read `data-asset-id`, `data-image-url`, and `data-is-latest-generated`. If it exists with `data-selected="false"`, treat the canvas as having no selected image. If the state node is missing or unusable, inspect canvas image nodes with `data-figurelabs-canvas-image="true"` and selected state such as `data-figurelabs-selected="true"` or `aria-selected="true"`, then read `data-figurelabs-asset-id`, `data-figurelabs-image-url`, and `data-figurelabs-is-latest-generated`. Preserve any additional returned identifiers. The page may still expose `window.figurelabsCanvas?.getSelectedImage()`, but the DOM/accessibility contract is the reliable automation contract.
2. Screenshot detection. Use this only when the DOM/accessibility contract is unavailable or unusable. Keep the current visual rule: an image is selected when it has a visible blue outline/selection box around it, or when a floating toolbar appears above that image.

After this fresh browser check, the user's modification request is assumed to apply to the selected image unless the user explicitly names another target. If the DOM contract reports no selected image, if screenshot fallback finds no selected image, or if the selected image is reported as latest generated, submit the edit request normally with the known `source_id` and `version_id`; the system will modify the last generated image by default. If the selected image is reported as not latest generated, or screenshot fallback shows that the selected image is not the last generated image, include the selected image itself in the edit request: pass its `data-image-url`, `data-figurelabs-image-url`, or other accessible image URL in `files` when available, or use the file upload/import tool first and pass the returned file reference. Identify the attached selected image in `instruction` as the image to modify. Do not choose a manual page operation based on this selection inspection.

Pass the user's instruction, target identifiers, requested ratio or structure, attachments, and a stable `idempotency_key`. When attaching a selected image, use only a real accessible image URL, FigureLabs file id, or uploaded file reference supported by the tool schema. Do not invent source/version IDs or image URLs.

Continue with the original job polling flow after submitting the edit. After the edit completes, apply [figurelabs-browser-handoff](../figurelabs-browser-handoff/SKILL.md): open or refresh the FigureLabs project before delivery while preserving any required context. On success, direct the user to the opened page and provide the project link without adding a chat image by default. For an explicit chat-image request or failed browser delivery, follow the preview-first and eligible local-file fallback there. Do not embed image_url/download_url directly in chat; report the actual browser/display outcome.
