---
name: figurelabs-onboarding
description: Connect to FigureLabs, introduce its capabilities, and initialize or reopen a workspace when starting with the plugin. Use for onboarding or an explicitly requested empty project; use the generation workflow for actual drawing requests.
---

# Start with FigureLabs

Use only this plugin's `figurelabs` MCP connection. Authenticate through the host's supported connection flow. Do not create duplicate registrations or switch environments when authentication fails. If a required tool is absent from discovery, report that limitation; do not invent a replacement API call.

## Dedicated task

Create a separate host task only when the user explicitly requests it, including through the plugin's English starter prompt. Do this before initializing the FigureLabs project. If supported, send the new task a self-contained prompt:

> This is the dedicated FigureLabs task requested by the user. Stay here; do not create another task. Use only the FigureLabs plugin's `figurelabs` MCP connection. Follow figurelabs-onboarding to initialize or reopen the requested workspace, then figurelabs-browser-handoff to open or refresh its page before delivery. Use the browser action's returned state; no extra quality review is needed. Introduce the returned capabilities in the user's language. Do not generate an image until requested. Keep the connection, workspace, project link and subsequent job/source/version IDs together.

Include the user's title, figure type, existing workspace and actual drawing request if supplied. If the host cannot create a task, continue in the current conversation. A remote MCP connection does not create a host task by itself.

## Current initialization contract

`figurelabs_project_bootstrap` currently creates an empty project and session, or reopens an existing workspace. It is not a welcome-page-only endpoint. Connecting/initializing the MCP protocol alone does not request project creation.

- For requested onboarding or an empty project, use a stable `idempotency_key`. Infer `figure_type` from the request: `illustration`, `plot`, or `flowchart`; general onboarding currently defaults to `illustration`. Reuse the same key and parameters when retrying the same intended creation.
- To reopen, send only `workspace_id` and optional `request_id`. Reopening resolves handoff again without creating a second workspace; it does not guarantee a fresh browser login token.
- `status=ready` means the project is ready, not that an image has been generated. Preserve `workspace_id`, `figure_type`, and `browser_handoff`; do not poll an empty project or invent a job ID.
- Each current workspace has one figure type. For a later request of another type, use a new project for that type rather than passing the incompatible workspace or changing its Agent.
- A drawing request can go directly through [figurelabs-generation](../figurelabs-generation/SKILL.md); bootstrap is not a mandatory extra step before every create.

## Open and introduce

Apply [figurelabs-browser-handoff](../figurelabs-browser-handoff/SKILL.md) to open or refresh the page and use the action's returned state. Do not add a screenshot or canvas inspection to normal onboarding. On browser failure, preview requires a real image; an empty project has none, so provide its project link and state the limitation. Explain the returned capabilities in the user's language, including that plots require real data. State that no image was generated or generation credits charged. Do not generate a sample to make preview possible.

les:

- Do not summarize, shorten, merge, or omit any section of the template.
- Preserve the order, headings, bullet groups, numbered canvas-learning steps, and four example requests.
- Translate the template naturally into the user's language when appropriate, but keep every semantic item. The English starter prompt alone does not override the user's established language.
- Replace only dynamic placeholders such as tool count, project title, browser outcome, project link, and credits status.
- Adapt discovered tools and returned capabilities only by filling dynamic details; do not remove template sections because a capability was not inspected.
- Describe canvas features as options in the web app, not as controls you have inspected or operated.
- Publication-ready is a goal supported by refinement, not a claim that an empty project or unreviewed output has passed quality checks.
- Preserve source data and describe any requested analysis or transformation explicitly.
- The examples are suggestions, not authorization to generate, download, inspect, or preview a sample.
- Do not add a chat image or generate a sample image.
- If bootstrap fails, do not use this success template; explain the actual failure instead.

Use the following template as the required onboarding response. Output all sections unless bootstrap failed. Translate naturally into the user's language, but do not omit, compress, or reorder the listed content:

> **FigureLabs is connected and ready.** All skills and seven MCP tools are currently loaded.
>
> FigureLabs helps you create and refine scientific figures for papers, presentations, and research communication using your research content, files, and data:
>
> - **Scientific illustrations:** Create mechanism diagrams, graphical abstracts, labeled figures, and research concept illustrations.
> - **Data plots:** Create charts from real CSV, XLS, or XLSX data while preserving the original source data.
> - **Flowcharts:** Turn Methods, experimental procedures, and research workflows into clear, editable flowcharts.
> - **Figure revisions:** Adjust colors, labels, layout, proportions, and overall visual style.
> - **Web canvas editing:** Continue arranging content, editing text, redrawing selected areas, and exporting in the canvas. Frame, shape, line, pencil, and comment tools are available from the **More** menu in the bottom toolbar.
>
> You can learn the common canvas features in this order:
>
> 1. **Upload an external image**  
>    Add a local or external image to the canvas as an editing target or reference asset.
> 2. **Mark an image**  
>    Mark an image on the canvas, then describe in the chat how it should be replaced or combined with another image.
> 3. **Use Frame for manual or automatic layout**  
>    Use Frame to organize image content. You can adjust the layout manually or ask AI to help arrange it automatically.
> 4. **Add canvas elements**  
>    Use the Text tool to add titles, labels, and descriptions. Open **More** in the bottom canvas toolbar to access **Frame, Shapes, Lines, and Pencil** for adding shapes, lines, and hand-drawn elements.
> 5. **Comments and collaboration**  
>    Select **Add comment** from the **More** menu in the bottom toolbar to comment on canvas content. You can ask AI to apply the comment as an image revision, or use comments to collaborate with others.
> 6. **Zoom the canvas**  
>    Zoom in to inspect local details or zoom out to review the overall layout, making it easier to switch between detailed editing and global composition.
>
> You can try requests such as:
>
> - "Create a graphical abstract from this paper."
> - "Create a publication-ready line chart from this CSV."
> - "Turn these Methods into an experimental flowchart."
> - "Revise this figure in a clean journal style."

Finish with the observed browser outcome and the real token-free project link, rather than inventing a link in the example. If opened, say the empty project is open for editing and no image was generated or generation credits charged. If opening failed or browser control is absent, say the connection succeeded but the page could not be opened here, and provide the link; an empty project has no image for preview. If authentication or bootstrap failed, explain the actual failure instead of using the success introduction.

Give the full introduction once per conversation, or again when the user asks for capabilities. Reopening a project, retrying bootstrap, polling, generating or editing should not repeat it. When the user already has a concrete drawing request, proceed with that request without inserting the full onboarding introduction. Do not store a permanent user-level "introduced" flag or create extra projects to deliver this message.

On `workspace_not_found`, report the access/deletion problem. On `workspace_type_mismatch`, resolve the requested type and target project. On `idempotency_conflict`, preserve the original creation parameters or use a new key only for an intentionally new project. Do not quietly replace an existing project after an error.
