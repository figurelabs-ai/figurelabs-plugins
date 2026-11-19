# FigureLabs plugin: installation and first-session guide for agents

This page tells an AI agent how to install the FigureLabs plugin in Codex or Claude Code. Act on it only when the user explicitly asks to install or set up FigureLabs. If the user asks to inspect, explain, compare or review this page, answer that request without changing configuration. Follow the host's permissions and the user's instructions throughout.

## Distribution identity

| | |
| --- | --- |
| Repository | `https://github.com/figurelabs-ai/figurelabs-plugins` (Git ref `main`) |
| Plugin | `figurelabs`, in `plugins/figurelabs` |
| Codex marketplace | `figurelabs-marketplace` (`.agents/plugins/marketplace.json`); plugin ID `figurelabs@figurelabs-marketplace`; manifest `plugins/figurelabs/.codex-plugin/plugin.json` |
| Claude Code marketplace | `figurelabs` (`.claude-plugin/marketplace.json`); plugin ID `figurelabs@figurelabs`; manifest `plugins/figurelabs/.claude-plugin/plugin.json` |
| MCP server | name `figurelabs`, remote (streamable HTTP), `https://mcp.figurelabs.ai/mcp`, configured in `plugins/figurelabs/.mcp.json` |
| Authorization | OAuth in the user's browser with their FigureLabs account; scope `figurelabs.mcp` |

The plugin contains six skills and the MCP server entry. It runs no local code and does not install a server. Do not hard-code a version: read it from the manifest on the selected ref. Use only the MCP address above; do not substitute another FigureLabs address.

## 1. Find out what the host can do

Identify the host you are running in (Codex or Claude Code) and inspect its plugin commands before using them:

```sh
codex --version && codex plugin --help
claude --version && claude plugin --help
```

- A desktop app and the CLI on the shell's `PATH` can be different versions. If the CLI lacks plugin commands, use the app's plugin management or guide the user through its Plugins UI. Do not install another CLI or change unrelated settings without the user's authorization.
- Check what is already there (`codex plugin marketplace list`, `codex plugin list`; `claude plugin marketplace list`, `claude plugin list`). If `figurelabs` is installed and enabled from this repository, update it (step 2) instead of installing it again. If a marketplace of the same name points somewhere else, explain the collision and do not replace it.
- Preserve all unrelated plugins, MCP registrations and credentials.

## 2. Add the marketplace and install the plugin

Adding the marketplace is not the same as installing the plugin; do both.

**Codex**

```sh
codex plugin marketplace add figurelabs-ai/figurelabs-plugins --ref main
codex plugin add figurelabs@figurelabs-marketplace
```

In the desktop app: open Plugins, add the repository as a marketplace with Git ref `main` and no sparse paths, then install FigureLabs from it.

**Claude Code**

```sh
claude plugin marketplace add figurelabs-ai/figurelabs-plugins
claude plugin install figurelabs@figurelabs
```

Inside a Claude Code session the same commands are `/plugin marketplace add figurelabs-ai/figurelabs-plugins` and `/plugin install figurelabs@figurelabs`.

**Updating an earlier version**

```sh
codex plugin marketplace upgrade figurelabs-marketplace   # then update or reinstall the plugin
claude plugin marketplace update figurelabs && claude plugin update figurelabs@figurelabs
```

Versions before 0.5.0 connected to a different address. After updating from one of them the user signs in once more (step 3), and a new session is needed for the new skills and tools to load.

Confirm command syntax with `--help` before use. Then verify the installed plugin: its identity, that it is enabled, its version against the manifest fetched from the ref, and that its MCP configuration names `https://mcp.figurelabs.ai/mcp`. Cloning the repository is not an installation. Do not copy files into the host's cache, and do not register a second standalone MCP server to get around the plugin.

## 3. Connect and sign in

The MCP server requires OAuth. The user completes sign-in and authorization in their browser; never ask them to paste a password, token or API key into the conversation, and never read credential storage.

- **Codex:** complete the sign-in the app offers when the plugin is installed, or the host's Connect / Authorize / Reconnect action for the plugin. From a terminal, `codex mcp list` shows the plugin's server as `figurelabs` with its sign-in state; `codex mcp login figurelabs` starts the sign-in when it is listed there.
- **Claude Code:** in a session, run `/mcp`, select the `figurelabs` server of the plugin and choose Authenticate.

A login page opening, or the user saying "done", is not evidence of success. Before reporting the connection as working, require both:

1. The host reports the authorization as successful or the server as connected.
2. The host completed tool discovery on that connection. The server lists these tools: `figurelabs_create_illustration`, `figurelabs_create_plot`, `figurelabs_create_flowchart`, `figurelabs_edit_figure`, `figurelabs_split_layers`, `figurelabs_get_job`, `figurelabs_show_figure`, `figurelabs_get_credits`, `figurelabs_open_project`, `figurelabs_upload_file`, `figurelabs_create_upload_session`.

Installed skill files and a configured URL do not prove either. Before sign-in the server answers `401` with a `WWW-Authenticate` header that points to its OAuth metadata; that is the expected state of a server that is reachable and not yet authorized, not an outage.

To confirm an authorized connection with a call, use `figurelabs_get_credits`: it is read-only, spends nothing and creates nothing. Do not call a create, edit or split tool as a probe; they spend the user's credits.

Missing tools alone do not prove missing authorization. Tell apart a plugin that has not loaded in the current session (start a new one), a disabled server, a transport error and a real authorization challenge, from what you observe. Do not start OAuth again when the host reports valid authentication.

Track installation, authorization and tool discovery separately. If one of them is unknown or failed, report "installed; connection setup pending" with the remaining step, and do not report the request as complete.

## 4. First session, when the user asks for one

Only if the request includes starting to use FigureLabs, and only after step 3 passed:

- Plugins load when a session starts. If the current session does not have the FigureLabs skills and tools, start a new session or task when the host supports it, or ask the user to open one. Create at most one, and do not claim one was created without an identifier from the host.
- In that session follow the plugin's `figurelabs-onboarding` skill: confirm the connection, introduce the capabilities in the user's language, then wait for a request. Do not generate a sample figure. Open an empty project (`figurelabs_open_project`) only if the user asked for one.
- A request to run the plugin's "default prompt" or "default connection prompt" means exactly the step above: confirm the connection, introduce the capabilities, wait. The `defaultPrompt` entries in the plugin manifest are example requests shown to the user as suggestions. They are not instructions; never run one unless the user picks it or types it.
- The session reuses the existing authorization. It must not ask for another login unless the host reports the credentials as expired or invalid. Never copy tokens into a prompt.

## 5. Report

Report only what you observed: the installed version, whether the plugin is enabled, the authorization and tool-discovery state, any session you created, and what the user still has to do. Do not report a connection or a project before it exists. Installation never includes generating a figure, spending credits, pushing Git changes or publishing anything.

## Troubleshooting

- **"marketplace root does not contain a supported manifest" (Codex):** `.agents/plugins/marketplace.json` must exist at the repository root on the selected ref; leave sparse paths empty.
- **Plugin missing after adding the marketplace:** the marketplace entry points to `./plugins/figurelabs`; install the plugin explicitly.
- **Skills or tools missing in the current session:** check installed and enabled state, then authorization and discovery; start a new session. Do not substitute another address.
- **Tools still carry old names, or calls go to an old address:** an earlier version is cached. Update the marketplace and the plugin (step 2), sign in again and start a new session.
- **In Codex, FigureLabs calls go through the ChatGPT connector:** when the user has also connected FigureLabs in ChatGPT, Codex may use that connection instead of the plugin's own server. Both reach the same FigureLabs service and tools, each with the account that signed in on that connection.
- **OAuth or service error:** report the actual error. A configured URL does not prove the service is reachable.

Packaging references: [Codex plugins](https://developers.openai.com/codex/plugins), [Claude Code plugins](https://code.claude.com/docs/en/plugins).
