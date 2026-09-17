---
name: figurelabs-file-import
description: Import attachments, accessible file URLs or local files for FigureLabs generation and editing, then pass the returned file references to the correct tool. Use for reference images and CSV/XLS/XLSX plotting data.
---

# Import the requested inputs

Use only this plugin's `figurelabs` MCP connection. Import files needed for the user's request and keep returned file_id, purpose, MIME type, expiry and connection together. Never reuse another environment's file ID. Uploading an input is not creating a project or generating a figure.

## Choose a supported source

- A valid FigureLabs file_id for this environment can be reused directly as `source=figurelabs_file`; do not upload it again unnecessarily.
- For an accessible HTTPS URL, call `figurelabs_upload_file` with `source=temporary_url` and `uri`. For an OpenAI attachment download URL, use `source=openai_file` and `download_url`, plus known filename/MIME information supported by the schema.
- For small available file bytes, `source=inline_base64` with `data` is supported. The decoded single-file limit is currently 1 MB; do not embed larger files into tool JSON or invent a download URL for a local path.
- For a local path and a host able to read/upload it, call `figurelabs_create_file_import_session` with purpose `reference` or `plot_data`. Follow [local upload](references/local-upload.md) using the returned endpoint, token header and limits. Creating the session alone does not upload the file.
- If the host cannot read or upload a local file and no accessible attachment URL exists, explain the limitation and request an attachment through the client's supported input flow. Do not manufacture data or a file_id.

Use `purpose=plot_data` for chart data. Plot consumes CSV/XLS/XLSX or actual structured data; an image of a chart is not its underlying data. Preserve original values, units and missingness. Resolve missing columns or ambiguous semantics before generating statistics. Other tool types may accept reference images/documents, but audio and video are not supported inputs.

## Confirm import and hand off

Check the upload result for errors and an actual FigureLabs file_id. For the local HTTP upload, inspect both the HTTP response and the business envelope; use `data.file_id` only after success. A session upload_token or an OpenAI file ID is not a FigureLabs file_id.

Pass the returned reference through the chosen generation tool's supported `files`, `data_file` or `data_files` parameter, for example `{"source":"figurelabs_file","file_id":"<returned file_id>"}`. Include other metadata only as required by the live schema. Resume [figurelabs-generation](../figurelabs-generation/SKILL.md) or the pending [figurelabs-canvas-edit](../figurelabs-canvas-edit/SKILL.md) workflow, retaining the user's original intent.

For an expired import token, create one replacement session if the upload is still needed and retry once. For an uncertain upload response, reuse any returned valid file reference; do not proceed with a guessed identifier. Never put import tokens in final replies or persistent plugin files. Upload endpoints are HTTP transfer destinations, not browser handoff URLs.
