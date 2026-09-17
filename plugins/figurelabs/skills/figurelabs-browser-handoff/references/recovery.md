# Recover only the failing stage

Use the originating `figurelabs` MCP connection and known job/project context. Supplement [figurelabs-browser-handoff](../SKILL.md) only when an actual problem occurs. Missing extra visual observation after a successful navigation is not an anomaly.

| Failure | Next action |
| --- | --- |
| No browser, or open/refresh failed | Attempt figurelabs_image_preview once when discovered and a real image URL exists, without downloading/rendering first. If still processing, retain the fallback intent until that job completes. Then provide the project link and truthful browser outcome. |
| Preview missing, ineligible or failed to render, with a real image URL | If the host permits local download and embedding, reuse a suitable local image of the same version or download once, verify it is a readable image, and embed the local file using supported host syntax. Otherwise provide ordinary project/file links and the limitation. Do not bypass host restrictions or retry preview recursively. |
| No actual image yet, or local download/display failed | Provide known project/file links and the actual limitation. For processing, retain fallback intent until the original job supplies an image. Empty projects have no image to preview or download; never invent one. |
| Evident login/error page | Treat handoff as failed and use the preview fallback. If browser repair is needed, use normal login or one relevant recovery below. |
| Unconsumed token expired with workspace_id | figurelabs_project_bootstrap with only workspace_id and optional request_id can resolve handoff once. Do not bootstrap a replacement project. |
| Consumed token, browser login lost | Reopen may reuse the consumed token and cannot guarantee login recovery. Use the direct project link and normal login; do not loop bootstrap. |
| Expired handoff without workspace_id | Keep project_url and preview fallback. Current status may not refresh handoff; do not replay generation or invent reopen-by-job arguments. |
| A relevant visible canvas problem or user feedback | Inspect only the affected target. Use canvas-edit for corrections; do not download/analyze every version. |
| Expired file URL when needed for preview/download | Query the original tool/job for a new signed URL and retry the necessary action once. Do not generate again. |
| Network error or rate limit while polling | Retry a query on the original job with the actual delay. Do not create another job or reset elapsed time. |
| Still processing at the waiting limit | Check the original job and report an unconfirmed terminal outcome. Preserve IDs and link; timeout alone does not authorize regeneration. |
| Terminal failed | Diagnose error_code/error_message/retryable. Repair the specific cause if possible. Retry submission with the original key for an uncertain outcome; a new charged generation requires supporting user intent/authorization. |
| Ownership/environment mismatch | Resolve the intended connection/target without rewriting hosts or borrowing IDs from another environment. |

Default to at most one justified recovery for the same cause; do not loop unchanged failures. On recovery, return to the request's fast or explicit delivery path. Browser fallback preview is a separate display attempt, not proof of successful navigation or a reason to perform full quality checks.

Fresh handoff tokens currently last about five minutes. Consumed token replay requires the same browser user and user type. Keep tokens out of replies and long-term storage. Always retain usable token-free project links.
