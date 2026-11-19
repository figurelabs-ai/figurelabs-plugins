# Upload a local file

For hosts that can read the file and run a command (Claude Code, Codex and similar).

1. Call `figurelabs_create_upload_session` with `purpose` (`plot_data` for a data file, `reference` for an image or document; `reference` when omitted). The result has the upload URL (`upload_url`), the token and its header name (`token`, `token_header`), the size limit (`max_bytes`), the expiry (`expires_at`, about 15 minutes) and a ready `curl` command. One session takes one file and its token works once; open one per file.
2. Check the file exists and is within the size limit.
3. Send `multipart/form-data` to the exact upload URL with the file in the field `file`, its real filename and MIME type, and the token in the returned header. The part's type matters: a CSV sent without `type=text/csv` is stored as a generic file and a plot will refuse it. Example:

   ```sh
   curl -sS -X POST "$UPLOAD_URL" \
     -H "$TOKEN_HEADER: $UPLOAD_TOKEN" \
     -F "file=@data.csv;type=text/csv"
   ```

   Common types: `text/csv`, `text/tab-separated-values`, `application/json`, `application/vnd.ms-excel` (XLS), `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` (XLSX), `image/png`, `image/jpeg`, `application/pdf`.

4. Read the `file_id` (`figfile_…`) from the JSON response and pass it to the tool: `file_refs` (or `data_file_ids` for a plot), `file_ref` for a layer split.

Keep the token out of replies, logs and files; pass it through an environment variable. Send it only to the returned upload URL. If the upload failed or the session expired, open one new session and try once more.
