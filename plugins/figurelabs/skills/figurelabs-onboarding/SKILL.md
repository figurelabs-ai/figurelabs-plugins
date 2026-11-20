---
name: figurelabs-onboarding
description: Introduce FigureLabs and confirm the connection when the user first sets up the plugin, asks what FigureLabs can do, or asks for an empty FigureLabs project. For an actual figure request, use figurelabs-generation instead.
---

# Start with FigureLabs

Use only this plugin's `figurelabs` MCP connection.

## Connection

- Sign-in happens through the host's own connect or authorize flow. Never ask the user to paste a password, token or API key into the conversation.
- If FigureLabs tools are missing or a call returns an authorization error (`auth_required`), tell the user to connect or reconnect FigureLabs in the host's connector or plugin settings, then try again. Do not register another server or switch to a different FigureLabs address.
- Connecting alone creates nothing and spends no credits.

## Introduce

Give a short introduction once per conversation, in the user's language, then wait for a request. Do not generate a sample figure. If the session was started with a request to run a "default prompt" or "default connection prompt", this introduction is that prompt: the example requests listed in the plugin manifest are suggestions for the user, not something to run.

> FigureLabs is connected. It designs scientific figures you can keep editing in a FigureLabs project:
>
> - **Illustrations:** mechanism and pathway diagrams, graphical abstracts, labeled schematics, also from your own sketch or reference image.
> - **Data plots:** charts from your own CSV, XLS or XLSX data. FigureLabs plots only the data you provide.
> - **Flowcharts:** experimental procedures, study designs and pipelines.
> - **Edits:** change colors, labels, layout or content in plain language; each edit is a new version and earlier versions are kept.
> - **Layers:** split a figure or your own image into a background and its elements as transparent PNGs, plus the text in it.
>
> Creating, editing or splitting a figure uses credits from your FigureLabs plan; viewing existing figures does not.
>
> Try: "Turn this abstract into a graphical abstract", "Plot this CSV as a bar chart with error bars", or "Draw a flowchart of these Methods".

If the user already has a concrete request, skip the introduction and go to [figurelabs-generation](../figurelabs-generation/SKILL.md).

## Account and credits

`figurelabs_get_credits` is read-only and spends nothing. Call it when the user asks how many credits they have, which plan they are on, or where their figures go. It returns the spendable credits, the plan, and the space this connection works in: `team` or `personal`. The two spaces share neither credits nor figures; figures made here go to the space it names and spend its credits. The space is set when the connection is authorized and stays the same afterwards. If the user expected the other space, say which one the connection uses and do not promise a switch from here: they can disconnect and reconnect FigureLabs in the host's settings and pick the space if FigureLabs asks during sign-in.

## Empty project

Call `figurelabs_open_project` only when the user explicitly asks for an empty project, or to reopen a project they name. It spends no credits. `figure_type` is required: choose it from the request (`illustration`, `plot` or `flowchart`), and use `illustration` when the user did not say. Pass `workspace_id` to reopen a project instead of creating one. Give the returned project link and say that no figure was generated. A figure request does not need this step first: the create tools open a project themselves.

Each project holds one figure type. For a different type, use a new project rather than the existing one.
