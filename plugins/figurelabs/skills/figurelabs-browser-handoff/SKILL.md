---
name: figurelabs-browser-handoff
description: Deliver FigureLabs results in the opened or refreshed project page. Use image preview and eligible local-file fallback when chat display is requested or browser delivery fails; inspect quality only when requested or needed for a specific problem.
---

# Open or refresh, then deliver

Use only this plugin's `figurelabs` connection. Preserve the originating tool, workspace, job/source/version IDs, project URL and known tab identity. Reuse already loaded rules and context for the current request.

## Required browser action for every purpose

Before delivering a project or result, **open the target project if it is not open, or refresh its existing tab**. This applies to drafts, downloads, publication work and repairs. A project opened while processing must still be refreshed when the completed result is delivered. Do not skip this step because the image is already visible or the user only wants a file.

Match the tab using the known frontend origin and project path, not merely its title. Use a visible built-in browser, preferably in the right panel when supported. Preserve necessary selection and unsaved changes before refreshing; do not discard user work. If refreshing cannot be done safely, treat it as unavailable and use the preview fallback below.

Use the browser action's existing return state to detect navigation errors or an evident login/error page. A successful action does not require a second screenshot, DOM inspection, version comparison or image analysis in the normal fast path. Do not call missing observation an error. Claim only what the existing evidence supports.

Ordinary processing polls are not separate deliveries. Do not open/refresh after each status call, repeat token consumption, or redo the same completed job's already delivered handoff. A new user request or new result has its own delivery action.

## Choose the display surface

After a successful target-project open/refresh, use that browser page as the default visual delivery. Tell the user to view the result in the already opened FigureLabs page and provide the project link. **Do not add a chat image by default.** A processing-time open alone does not establish successful delivery of the completed result.

Only request chat image display when the user explicitly asks for it or browser delivery is unavailable/failed. Do not use `image_url` or `download_url` as Markdown image sources, even when their URL looks like an image. These are resources for preview/download; ordinary file links remain allowed. Do not duplicate an image already displayed by the host automatically.

The returned `chat_image_display_policy` is guidance, not observed client state: `browser_first` is the normal delivery policy; `preview_required` identifies a requested preview response. The client determines browser/render success from available evidence.

## Links and fallback

- Navigate to the exact `browser_handoff.url`; let the frontend consume its token in the browser. Never pre-consume it through a shell/HTTP client or publish it in a final reply.
- Keep `browser_handoff.project_url` as the continue-editing link with all tracking parameters intact. Do not rewrite the host, client or query, or build a project path from workspace/job/source IDs. API and frontend domains may differ.
- `image_url` and `download_url` are signed image resources, not project pages. Retain them and earlier handoff fields when a later status response omits context. A top-level session_url is not guaranteed.
- If chat display is explicitly requested, or there is no browser capability, or opening/refreshing fails (including an evident login/error page), **attempt `figurelabs_image_preview` once as soon as the actual image URL is available**. Do not download or locally render the original as a prerequisite.
- Call preview only if discovered on this connection. Use the returned HTTPS image_url, or the original image download_url as its image_url input; pass known original IDs and MIME type as supported. Do not invent an image URL. For an empty project, a failed generation with no image, or a still-processing job, keep the project link and pending fallback intent; try preview after that same job supplies an image.
- Do not send browser_handoff back into preview just to display an image. A token-free session_url may be supplied if supported, but always give the project link separately. Preview success does not confirm the browser opened or the canvas updated, does not change generation credits, and must not trigger another handoff/preview cycle.
- Display preview using the host's native image content or preview component. Do not turn the returned remote URL into a Markdown image. Tool completion alone does not prove the host rendered it; use available render results or explicit client/user feedback, without an extra inspection loop.
- If preview is missing, ineligible or fails to render, and the host permits downloading and embedding local files, reuse a suitable local image of the same version or download the actual image URL once. Verify the download succeeded and is a readable image, not an HTML error page. Embed the existing local file using the host's supported syntax (an absolute local path where supported); never fabricate a path or use a server-side path as a client-local file. This is a display fallback, not a full quality review.
- If local download/display is unsupported, restricted or fails, explain the limitation briefly and provide actual project/file links as ordinary links. Do not bypass host media restrictions. Never generate again to fix browser or preview failures, or loop preview/download after a successful display.

## Fast delivery and optional checks

Normally direct the user to the opened browser page with the project link, then finish. No proactive original-image download, extra screenshot, pixel/DPI inspection or full visual review. The eligible local-file fallback above is an exception to the default no-download rule. Preview and local-file fallback are display actions, not quality reviews.

Load [delivery checks](../figurelabs-generation/references/delivery-checks.md) only for requested export/download, explicit formal use or quality inspection. For an actual failure, load [recovery](references/recovery.md). For user feedback about an existing figure, use [figurelabs-canvas-edit](../figurelabs-canvas-edit/SKILL.md) to inspect only the affected target or area.

Use the user's language. Report "Generated" based on the original job's completed status; report browser opening/refreshing based on the action result. Say quality was checked only after actually checking it. On browser failure, explain that preview was attempted/shown as applicable and include the token-free project link. Do not add an unrequested unverified-quality disclaimer to every normal delivery.
