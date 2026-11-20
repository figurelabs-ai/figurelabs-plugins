---
name: figurelabs-edit
description: Change an existing FigureLabs figure. Use when the user asks to modify a figure created earlier (colors, labels, layout, adding or removing elements, style), refers to "the figure", "this diagram" or "the last one", or gives feedback on a FigureLabs result.
---

# Edit a figure

Use only this plugin's `figurelabs` MCP connection.

- Use `figurelabs_edit_figure`, not a new create call. An edit keeps the figure's history: it adds a new version and earlier versions stay.
- Pick the target from the conversation: by default the figure most recently created or edited, otherwise the one the user names or describes. Pass its `source_id`. Pass `version_id` only when the user wants to edit a specific earlier version; omit it for the latest.
- If several figures fit the user's words, ask which one. Never invent a `source_id` or `version_id`.
- Write `instruction` as concrete changes: what to change, where, and to what ("make the arrow from STAT3 to the nucleus red and thicker; rename 'Cmpd X' to 'Compound X'"). Combine the user's requested changes for the same figure into one edit.
- Changing the figure type (for example a diagram into a bar chart) is not an edit; create a new figure with the matching create tool.
- An edit spends credits like a create. Follow the credits rule in [figurelabs-generation](../figurelabs-generation/SKILL.md), then [figurelabs-delivery](../figurelabs-delivery/SKILL.md) to wait for the result.

## Reference images

When the user gives an image the edit should follow ("use this logo", "match the style of this panel"), pass it with the edit and say in `instruction` what to do with it. Up to 5 files.

- ChatGPT attachments go in `attachments`.
- Elsewhere, pass `file_refs`: a FigureLabs file ID (`figfile_…`) from [figurelabs-file-import](../figurelabs-file-import/SKILL.md), or a public `https://` link.

## Marked regions

`regions` says where to change the figure: boxes as fractions of the image from its top-left corner (`x`, `y`, `width`, `height`; width and height 0 for a point), each with a `note` saying what to change there, up to 10. A request sent from the FigureLabs workbench already carries the user's marks: pass them as given. Otherwise describe the place in `instruction` rather than estimating coordinates.

## When the FigureLabs canvas is open in a browser

Only in hosts that control a browser (for example the Codex in-app browser) and only if the user points at something on the canvas ("the selected image", "this one"): read the selected image from the page's `#figurelabs-selected-image-state` element (`data-selected`, `data-asset-id`, `data-is-latest-generated`). If it is the latest generated figure, edit it as above. If it is a different image, ask the user which figure to edit; an image without a `source_id` from this conversation cannot be the target of an edit. Do not operate the canvas yourself.
