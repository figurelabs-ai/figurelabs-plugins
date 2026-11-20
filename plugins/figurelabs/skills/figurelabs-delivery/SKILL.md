---
name: figurelabs-delivery
description: Wait for a FigureLabs create, edit or layer split job and present the result. Use after any FigureLabs create, edit or split call, when the user asks how a figure is coming along, or when they want to see an earlier figure again.
---

# Wait for and present the result

Create, edit and split calls start a job; a figure usually takes one to a few minutes, a layer split two to five ([figurelabs-layers](../figurelabs-layers/SKILL.md)). Keep `job_id`, `source_id`, `version_id`, `workspace_id` and `project_url` from every result, and keep earlier values if a later result omits them.

## Hosts that show FigureLabs cards

ChatGPT and the Claude apps (web, desktop) render a FigureLabs card under the tool call. The call returns a `job_id` at once; the card follows the job itself and shows the finished figure, a link to the project and, after an edit, the earlier version.

- Do not call `figurelabs_get_job` in a loop and do not wait in the conversation. Tell the user the figure is being generated in the card and finish your turn.
- Call `figurelabs_get_job` once only when the user asks about the result or the card shows an error or does not appear.
- Do not draw the figure yourself (no Mermaid, ASCII art or generated image) while the card is working.

## Hosts without cards

Terminal and coding agents (Claude Code, Codex and similar) have no card.

- The create, edit or split call waits up to about 25 seconds and may already return the finished figure. If it returns `status: running` or `queued`, call `figurelabs_get_job` with the same `job_id` after `retry_after_seconds`. `figurelabs_get_job` also waits up to about 25 seconds before it answers, so a figure takes a few calls; keep using the `retry_after_seconds` of each result and do not poll faster.
- While waiting, tell the user it is still generating; do not start other FigureLabs jobs for the same request.
- After about 10 minutes still running, stop polling, give the project link and say the job is still in progress. A long wait is not a failure and is no reason to generate again.

### Open the project where the host has a browser

Without a card, the project page is where the user sees the full result: the full-size figure, its earlier versions and layers, and the tools to keep editing.

- If the host gives you a browser the user can see (for example the Codex in-app browser), open the project there once the figure or split has finished. Do it by default, without being asked.
- Open `handoff_url` when the result has one: it signs that browser in as the user and lands on the project. Without it, open `project_url`.
- `handoff_url` is a one-time sign-in link. Use it only to open the browser. Never put it in a reply, a file, a note or another tool call; the link you give the user is always `project_url`.
- Open each project once. If its page is already open, leave it as it is: do not reload it, since that can discard what the user is doing on the canvas. After an edit, say that the new version is in the project.
- Still present the result in the conversation as described under Present; the browser adds to the reply, it does not replace it.
- If the page still asks the user to sign in, let them; never fill in or ask for credentials.
- With no such browser (a plain terminal), give `project_url` and do nothing else: leave `handoff_url` unused, and do not start a system browser or any other program to show the figure.
- Opening the page is all you do there: do not click, type or otherwise operate the canvas.

## Present

- Say what was created or changed, give the project link from the result (`project_url`) and the credits charged when reported (`credits_charged`).
- Leave `image_url`, `handoff_url` and other signed links out of the reply, as images and as links; they expire, and `handoff_url` signs in whoever opens it. Give them only when the user asks to download the file. To show a figure again later, call `figurelabs_show_figure` with its `source_id` (and `version_id` for a specific version). It is read-only and spends no credits. If it answers that an earlier version cannot be shown yet, show the latest version and say so.
- Never send the same request again to get a new link or a better display.

## Failures

Errors carry a `code` and a message that says what to do next; follow the message.

| `code` | Do |
| --- | --- |
| `job_failed` | Explain FigureLabs' reason (`detail`). With `retryable: false`, the same request fails the same way: change it or stop. Otherwise submit again only if the user wants to, with a new `idempotency_key`. |
| `insufficient_credits` | Say the FigureLabs credits are insufficient and give the plans link from the error (`pricing_url`). Do not retry. If the user expected to have credits, call `figurelabs_get_credits`: it shows the balance and whether this connection works in their team or personal space, which do not share credits. |
| `unavailable`, `rate_limited` | Temporary. Wait `retry_after_seconds`, then check the same job, or call the same tool again with the same arguments and the `idempotency_key` the message names, so the request is not run and charged twice. Do not create a new job. |
| `idempotency_conflict` | An earlier attempt under this key may already be running: check it with `figurelabs_get_job`, or retry with exactly the original arguments and key. |
| `idempotency_abandoned` | FigureLabs will not finish the request under this key. Do the check the message names (for an edit, `figurelabs_show_figure` to see whether a newer version appeared), then submit once with a new key. |
| `invalid_input`, `invalid_file` | Fix the argument or file the message names and call again. The message lists the allowed values. |
| `content_rejected` | Explain the limit; do not rephrase the request to get around it. |
| `auth_required` | Ask the user to reconnect FigureLabs in the host's connector or plugin settings. |
| `not_found`, `forbidden` | The ID is wrong, or the job or figure belongs to another account or space. Say so and do not guess other IDs. |
