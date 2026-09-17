---
name: figurelabs-generation
description: Create FigureLabs illustrations, real-data plots and flowcharts with fast delivery by default, and follow existing jobs. Add file/quality checks only for explicit delivery needs or diagnose actual failures; use canvas-edit for changes to existing figures.
---

# Generate with the work the request needs

Use only this plugin's `figurelabs` MCP connection and discovered tools. Keep connection, tool name, figure type, optional workspace, original submission time, job/source/version IDs, handoff, project URL and delivery outcome together. Do not mix environments or erase earlier fields when status omits them.

## Choose the workflow once per user request

- Ordinary drawing, drafts, previews, or no stated purpose: use the fast path. Do not ask the user to choose a purpose or infer publication use merely because the subject is scientific.
- Explicit download/export or use in a paper, presentation or publication: read [delivery checks](references/delivery-checks.md) and perform only the checks relevant to that intent.
- Actual failed status, timeout or generation anomaly: read [recovery](../figurelabs-browser-handoff/references/recovery.md) and diagnose the failing stage. Do not run every possible download or quality check.
- Feedback about text, labels, layout or another existing-figure change: use [figurelabs-canvas-edit](../figurelabs-canvas-edit/SKILL.md) for focused correction, preferring suitable Text Edit, Region Redraw or layout controls.

Decide or change the path based on user intent and observed problems, not every tool return. Reuse instructions already read for this request. Browser open/refresh before delivery is mandatory in all paths.

## Submit the appropriate job once

| Request | Tool | Input rule |
| --- | --- | --- |
| Mechanisms, experimental illustrations, graphical abstracts | `figurelabs_illustration` | Reference images when needed |
| Charts and statistical visualizations | `figurelabs_plot` | Actual user data or CSV/XLS/XLSX; never fabricated statistics |
| Steps, nodes, arrows, branches and feedback loops | `figurelabs_flowchart` | Describe the intended semantic structure |

Follow the live schema. Load [figurelabs-file-import](../figurelabs-file-import/SKILL.md) only when input files need importing. An existing-figure edit requires the canvas-edit workflow, not a substitute create.

For create, reuse a known workspace only if its figure_type matches. Without workspace_id, create starts the corresponding project; no extra bootstrap is required. Explain a requested type switch that needs another project and preserve the old context.

Use a stable idempotency_key per intended create/edit. For an uncertain submission, repeat the same request/key or query the known job; do not change the key to evade idempotency conflicts. Preserve the first handoff and IDs. You may open the project once while processing; no inspection/download pipeline follows that early opening. Record browser failure for preview fallback after an image becomes available.

## Follow the original job

Query the same tool with operation=status and the original job_id. From the original submission time, poll about **every 5 seconds during the first 90 seconds, then every 10 seconds**. Respect actual rate-limit/retry delays. Retries and browser actions do not reset the timer; keep waits interruptible and updates useful.

- processing: still running; do not trigger delivery or repeat navigation on each poll.
- completed: original generation finished; retain file URL, MIME type, version and actual credits_charged, then deliver once.
- failed: diagnose the business failure even when isError=false or HTTP is 200.
- isError=true or a transport/auth error: distinguish a failed query from a failed generation, and recover the original job when possible.

At 10 minutes, check the same job once more and diagnose an unresolved result. Client timeout is not a confirmed terminal failure. Preserve the job/link and do not automatically launch another charged generation. New generation after a confirmed failure must be supported by the user's intent/authorization and the actual retry conditions.

## Deliver and finish

Apply [figurelabs-browser-handoff](../figurelabs-browser-handoff/SKILL.md): open or refresh before final delivery. On success, tell the user to view the result in the opened browser page and provide the project link; do not add a chat image by default. For an explicit chat-image request or browser failure, use preview first, then the eligible client-local file fallback if preview is unavailable or fails. Never use image_url/download_url as Markdown image sources. In the fast path, finish after delivery; do not download or analyze the original merely because a URL exists.

Current completed responses may contain only download_url and omit image_url, workspace_id or browser_handoff. Retain earlier context for that same job. Preview completion/zero preview credits must not overwrite the generation outcome or cost. Repeat quality checks only when the relevant version, export requirements or user feedback changes.
