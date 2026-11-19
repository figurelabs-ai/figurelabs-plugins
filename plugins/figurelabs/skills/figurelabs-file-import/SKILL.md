---
name: figurelabs-file-import
description: Bring user files into FigureLabs before creating or editing a figure. Use when the user attaches or points to a data file (CSV, TSV, JSON, XLS, XLSX) for a plot, or a reference image or document for an illustration, an edit or a layer split.
---

# Import files

Use only this plugin's `figurelabs` MCP connection. Import only files the user has explicitly provided or named for this request. Do not search the user's computer or account for other files.

## Choose how

| Where the file is | Do |
| --- | --- |
| Attached in ChatGPT | Pass the attachment straight to the tool that uses it: `attachments` on `figurelabs_create_illustration`, `figurelabs_create_plot` and `figurelabs_edit_figure`, `attachment` on `figurelabs_split_layers`. ChatGPT supplies the file object. |
| A public or pre-signed `https://` link | Pass the link straight in the tool's `file_refs` (`file_ref` for a split). Use `figurelabs_upload_file` with `url` only when you need the file ID first, or the link has no file extension (then give `filename`). |
| Small content already in the conversation (at most 1 MB) | `figurelabs_upload_file` with `base64` and `filename` (with extension); `mime_type` when the extension does not say it. |
| A local file in Claude Code, Codex or another agent that can run commands | `figurelabs_create_upload_session`, then upload as described in [local upload](references/local-upload.md). |
| Attached in a Claude chat | Use it only if the host provides a URL or the content; otherwise ask the user to paste the data, share a link or upload the file at figurelabs.ai. |

- Set `purpose` to `plot_data` for chart data and `reference` for images or documents.
- Limits: 20 MB per file, up to 5 files per call. Plot data: CSV, TSV, JSON, XLS or XLSX. References: images or PDF. A layer split takes one PNG or JPEG.
- Keep the returned `file_id` (`figfile_…`) and pass it in `file_refs` (or `data_file_ids` for a plot, `file_ref` for a split). When a create or edit call imports files itself, its result names the new file IDs: reuse them on a retry instead of importing again. An upload session token, a ChatGPT file ID or a guess is not a FigureLabs file ID.
- Imported files are kept for about a day; import again after that.
- `figurelabs_create_flowchart` takes no files; put what matters from a document into its prompt.

Importing a file spends no credits.

## Data for plots

Keep the user's values, units and missing entries as they are. A screenshot of a chart is not data. If the file has several sheets or ambiguous columns, ask which to use before plotting. Numbers typed or pasted in the conversation go straight into `figurelabs_create_plot`'s `data` as CSV text with a header row, without uploading a file.

Then continue with [figurelabs-generation](../figurelabs-generation/SKILL.md), [figurelabs-edit](../figurelabs-edit/SKILL.md) or [figurelabs-layers](../figurelabs-layers/SKILL.md).
