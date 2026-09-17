# Upload a local file into FigureLabs

This flow applies only when the host can read the requested file and perform an HTTP upload. Use an available documented file-upload capability or local HTTP runtime. The plugin does not require a particular shell or external helper installation.

1. Obtain a session from this plugin's `figurelabs` connection with `figurelabs_create_file_import_session`. Preserve `endpoint`, `upload_token`, `token_header`, `expires_at`, `max_file_count`, `max_size_mb`, and `allowed_types`.
2. Verify the local file and size against the returned limits. The server's current hard limit is 20 MB; the session may impose a lower limit. A local path is not a server-accessible URL.
3. POST multipart/form-data to the exact returned endpoint. The multipart field is `file`; include the real filename, bytes and MIME type. Set the returned token_header to the upload_token (currently X-FigureLabs-Import-Token). Let the HTTP library set the multipart boundary.
4. Use the endpoint from the same environment that issued the token. Do not send the token to a rewritten hostname or forward it through redirects to another origin. Do not print it in logs or expose it in a final reply; pass it through the runtime's structured input or a temporary process environment when possible.
5. Check HTTP success and the FigureLabs envelope `code=0`. The returned `data` contains file metadata, including file_id. Retain those metadata, not the token, for generation.

If the host provides the FigureLabs upload helper, it may be used according to its actual documented arguments. Do not assume a helper exists merely because an import-session tool is available. If a required host capability is missing, use an accessible attachment/HTTPS source or report the limitation instead of inventing a successful upload.
