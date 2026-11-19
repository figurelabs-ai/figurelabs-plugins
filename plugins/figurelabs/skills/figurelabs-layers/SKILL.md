---
name: figurelabs-layers
description: Split a figure into layers with FigureLabs. Use when the user wants a figure or image separated into its background and elements, transparent PNGs of single parts, the parts' positions, or the text labels in an image, for example to rearrange or restyle elements in slides or another editor.
---

# Split a figure into layers

Use only this plugin's `figurelabs` MCP connection.

`figurelabs_split_layers` works like Split Layers in the FigureLabs editor: it splits one image into a background and element layers (each a transparent PNG with its position) and reads the text in the image.

## The image

- A FigureLabs illustration: pass its `source_id`, by default the figure most recently created or edited in the conversation. Plots and flowcharts are not split.
- An image the user provides (PNG or JPEG): `attachment` where the host passes files (ChatGPT), or `file_ref` with a `figfile_…` ID from [figurelabs-file-import](../figurelabs-file-import/SKILL.md) in Claude Code and Codex, or with a public `https://` link. The split is recorded in a new illustration project unless you pass a `workspace_id`.
- Give exactly one image: `source_id`, `attachment` or `file_ref`. `version_id` goes only with `source_id`. Never invent a `source_id`.

## Mode

- `auto` (default): FigureLabs picks the layers.
- `custom`: when the user names parts to split out on their own. It needs `regions`, an `instruction`, or both; `regions` are refused in `auto`. Pass `regions` as fractions of the image from its top-left corner (`x`, `y`, `width`, `height`, up to 16, each side at least 0.02) and/or an `instruction` ("keep each arrow with its label").
- A request sent from the FigureLabs Layers panel already names the figure, `mode "custom"` and the regions: pass them as given.

## Credits and time

- A split is charged per layer it produces: 5 credits each in `auto`, 7 in `custom`; the total is known only when it finishes. Say so before the first split in a conversation, as in the credits rule of [figurelabs-generation](../figurelabs-generation/SKILL.md).
- It usually takes 2–5 minutes. Where FigureLabs cards show (ChatGPT, Claude apps), the card follows the job and then shows the layers: the user can select one, download it, or open the full Layers panel to hide layers, compare with the original and see the recognized text. Tell the user and finish your turn. Without cards, wait as in [figurelabs-delivery](../figurelabs-delivery/SKILL.md).

## Present

- Say how many layers there are and name them bottom first; mention the recognized text when it helps; give the credits charged.
- Leave the layers' image links out of the reply unless the user asks to download them; they expire. `figurelabs_get_job` with the same `job_id` returns fresh ones.
- A split does not change the figure. `figurelabs_edit_figure` on the split's `source_id` edits the original image as a whole, not one layer; to change one element, describe it in an edit.
