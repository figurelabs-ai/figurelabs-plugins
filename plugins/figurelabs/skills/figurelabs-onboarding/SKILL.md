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

## First successful introduction

For the first successful onboarding in the current conversation, give a welcoming, substantive introduction rather than only a status line and a link. Use the user's language; the English starter prompt alone does not override their established language. Include a connection confirmation, a short product introduction, the five capability areas below, four practical example requests, and the actual browser outcome with the project link. Keep this introduction in the dedicated task if one was created.

Adapt the example to discovered tools and returned capabilities. Describe canvas features as options in the web app, not as controls you have inspected or operated. Publication-ready is a goal supported by refinement, not a claim that an empty project or unreviewed output has passed quality checks. Preserve source data and describe any requested analysis or transformation explicitly. These examples are suggestions, not authorization to generate, download or inspect a sample.

Use this English example as a content guide, translating naturally when appropriate:

> **FigureLabs is connected and ready.**
>
> FigureLabs helps you create and refine scientific figures for papers and presentations from your research context, files, and data.
>
> Here's what I can help you do:
>
> - **Scientific illustrations:** create mechanism diagrams, graphical abstracts, labeled figures, and visual explanations of your research.
> - **Data plots:** turn CSV, XLS, or XLSX files into charts using your real data, while preserving the original source data.
> - **Flowcharts:** convert Methods, experimental procedures, and workflows into clear, editable flowcharts.
> - **Figure revisions:** refine an existing result by changing colors, labels, layout, proportions, or visual style.
> - **Canvas editing:** continue in FigureLabs for hands-on arranging, text changes, region redraws, framing, and export, where supported.
>
> Try saying:
>
> - "Create a graphical abstract from this paper."
> - "Create a publication-ready plot from this CSV."
> - "Turn these Methods into an experimental flowchart."
> - "Revise this figure to use a clean journal style."

Finish with the observed browser outcome and the real token-free project link, rather than inventing a link in the example. If opened, say the empty project is open for editing and no image was generated or generation credits charged. If opening failed or browser control is absent, say the connection succeeded but the page could not be opened here, and provide the link; an empty project has no image for preview. If authentication or bootstrap failed, explain the actual failure instead of using the success introduction.

Give the full introduction once per conversation, or again when the user asks for capabilities. Reopening a project, retrying bootstrap, polling, generating or editing should not repeat it. When the user already has a concrete drawing request, proceed with that request without inserting the full onboarding introduction. Do not store a permanent user-level "introduced" flag or create extra projects to deliver this message.

On `workspace_not_found`, report the access/deletion problem. On `workspace_type_mismatch`, resolve the requested type and target project. On `idempotency_conflict`, preserve the original creation parameters or use a new key only for an intentionally new project. Do not quietly replace an existing project after an error.
