# Checks triggered by a delivery request

Use only the originating `figurelabs` connection and result. These checks supplement [figurelabs-generation](../SKILL.md); load them only for explicit download/export, formal use or quality inspection. They do not change browser-first display or the preview/local-file fallback in [figurelabs-browser-handoff](../../figurelabs-browser-handoff/SKILL.md). A download/export request alone does not request a chat image; provide the requested file alongside the project link.

## Download or export

Reuse an existing suitable local output before downloading the same version again. Obtain the requested file from the returned signed URL using a supported host capability. Check that it is the requested real format, is readable, and matches explicitly requested dimensions or transparency; an HTML error page is not an image, and renaming an extension is not conversion. Inspect converted output when conversion is required. A simple PNG download does not require scientific-content review.

If the host cannot download or convert, say so and give the real available file/project links. Do not claim a file was saved without evidence or bypass host media restrictions.

## Formal use or requested visual review

Check relevant labels, clipping, overlap, layout and the user's stated requirements. Prefer an already available image or canvas view. Read the original only when resolution or file metadata is necessary. Ask for a missing specification only when it affects delivery; do not invent journal requirements.

Do not infer scientific correctness or publication readiness from completed status, pixel dimensions or DPI. State the checks actually performed and any material unresolved issue. If a specific defect needs correction, use figurelabs-canvas-edit for that target instead of regenerating everything.

## Reuse evidence

Reuse downloads and checks for the same unchanged version within this request. Changed export settings, a new image version or new feedback require only the affected checks. After those checks and the required browser delivery/fallback, finish without unrelated additional analysis.
