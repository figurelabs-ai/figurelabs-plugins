---
name: figurelabs-generation
description: Create scientific figures with FigureLabs. Use when the user asks for a mechanism diagram, schematic, graphical abstract, chart or plot of their data, or flowchart, wants a sketch or an existing image turned into a clean figure, and offer it when the user shares a paper abstract, Methods text, protocol or a data file (CSV, XLS, XLSX) that a figure would explain.
---

# Create a figure

Use only this plugin's `figurelabs` MCP connection and follow each tool's live schema.

## Pick the tool

| The user has or wants | Tool |
| --- | --- |
| A mechanism, pathway, cell or device schematic, graphical abstract, concept figure; a hand sketch to turn into a figure; an image to redraw or clean up | `figurelabs_create_illustration` |
| A chart of real data: an attached or uploaded data file, or numbers typed in the conversation | `figurelabs_create_plot` |
| Steps, decisions and branches: protocols, study designs, analysis pipelines | `figurelabs_create_flowchart` |
| A change to a figure made earlier | `figurelabs_edit_figure`, see [figurelabs-edit](../figurelabs-edit/SKILL.md) |
| A figure or image separated into layers | `figurelabs_split_layers`, see [figurelabs-layers](../figurelabs-layers/SKILL.md) |

- Data plots use only data the user supplied. Never invent values, sample sizes, error bars or p-values, and never read numbers off a chart image to re-plot them as data. If columns, units or groups are unclear, ask before plotting.
- Reuse a `workspace_id` only if that project has the same figure type. Without one, the tool starts a new project.

## Settings

Pass a setting only when the user asked for it or the material clearly calls for it; a value outside the list is refused with the allowed ones. Everything else about the look (colors, mood, a journal's style) goes in the prompt.

| Parameter | Tools | Values |
| --- | --- | --- |
| `style` | illustration, flowchart | `Flat` (default), `3D`, `2.5D`, `hand_drawn`, `line_art` |
| `ratio` | illustration, flowchart, plot | `16:9`, `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `21:9`; omit to let FigureLabs choose |
| `chart_style` | plot | `line`, `bar`, `scatter`, `area`, `histogram`, `box`, `violin`, `heatmap`; omit (or `auto`) to let FigureLabs choose |
| `language` | all three | Language of the labels, for example `en` or `zh` |
| `structure` | flowchart | The exact `nodes` (`id`, `label`) and `edges` (`from`, `to`, optional `label`) when the user has given them; omit to let FigureLabs work them out from the prompt |

## Files

- **Reference images for an illustration** (a sketch, a reference figure, an image to redraw): pass them in `attachments` (ChatGPT) or `file_refs` (a `figfile_…` ID or a public `https://` link), up to 5, and say in the prompt what to do with them ("follow this sketch", "match this style", "redraw this image cleanly").
- **Data for a plot:** `attachments` (ChatGPT), `file_refs` or `data_file_ids` for files, or `data` for CSV text the user typed or pasted. A plot needs at least one of them.
- A flowchart takes no files.
- For local files and file IDs, see [figurelabs-file-import](../figurelabs-file-import/SKILL.md).

## Use the conversation

The model writes the prompt; the user should not have to.

- When the user pastes an abstract, Methods text, protocol or results, offer one concrete figure in a sentence, for example "I can turn this into a graphical abstract: problem → approach → key result" or "a flowchart of the sample-preparation steps". Generate only after the user agrees.
- When the user shares a data file, offer a plot that fits the data (for example group means with error bars, a time course, a correlation) and confirm the chart type and columns if they are not obvious.
- Write a detailed prompt from the conversation: subject, every component and its label, relations and arrows, order or layout, emphasis, the audience (paper, poster, slides) and any style wishes the user gave. Use the terminology of the user's field. See [prompt writing](references/prompt-writing.md).
- Keep labels in the user's language unless they ask for another, and pass it in `language`.

## Credits

Creating a figure spends credits from the user's FigureLabs plan. Before the first create or edit in a conversation, say so in one sentence together with what you are about to generate. If the user asked for the figure directly, go ahead in the same turn; if you proposed it, wait for agreement. Results report `credits_charged`; mention it when it is present.

`figurelabs_get_credits` is read-only and free: it returns the spendable credits, the plan, and whether this connection works in the user's team or personal space. The two spaces share neither credits nor figures, and figures made through this connection go to the space it names. Call it when the user asks about their balance or where their figures go, or before a large batch; not before every figure, since the create tools report a shortfall themselves. On an `insufficient_credits` error, say the credits are insufficient and give the plans link from the error; do not retry.

## Submit once

- Call the tool once per intended figure. Omit `idempotency_key` on a first call; the server picks one.
- When a call fails with a temporary error, its message names the key it used: call again with the same arguments and that `idempotency_key`, so FigureLabs does not run and charge the request twice. Use a new key only for a new or changed request.
- Never submit again to fix a display or waiting problem. Follow the existing job instead.
- Then follow [figurelabs-delivery](../figurelabs-delivery/SKILL.md) to wait for and present the result.
