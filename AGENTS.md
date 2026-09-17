# FigureLabs for Codex — installation and first-session guide

This page describes installation only when the user explicitly asks to install or set up FigureLabs. If the user asks to inspect, explain, compare, or review this page, answer that request without changing configuration or creating a task. Follow the host's permissions and the user's instructions throughout.

## Distribution identity

- Repository: https://github.com/figurelabs-ai/figurelabs-plugins.git
- Git ref: main
- Marketplace: figurelabs-marketplace
- Plugin: figurelabs
- Qualified plugin ID: figurelabs@figurelabs-marketplace
- Marketplace manifest: .agents/plugins/marketplace.json
- Plugin directory: plugins/figurelabs
- Plugin manifest: plugins/figurelabs/.codex-plugin/plugin.json
- Bundled MCP configuration: plugins/figurelabs/.mcp.json
- Expected MCP server name: figurelabs
- Expected remote MCP URL: https://api.figurelabs.ai/plot-mcp

This is the production environment. It distributes five workflow skills and a remote MCP connection. It does not install or deploy the FigureLabs backend. Use an account with access to the production service. Do not substitute development or production connections. Generation/editing is subject to that service's permissions, quotas, and credit rules.

## 1. Identify supported installation capabilities

Inspect the client's available plugin-management tools or documented CLI. If a local terminal is available, check:

    codex --version
    codex plugin --help

Do not assume the shell's Codex CLI and the desktop app are the same version. An older standalone CLI may lack plugin commands even when the desktop app can install plugins. If CLI plugin management is absent, use supported desktop plugin-management tools or guide the user through the Plugins UI. Do not install another CLI or modify unrelated settings without appropriate user authorization.

Use available list/read operations to check the installed marketplace, plugin, enabled state, and authentication status. If CLI help exposes these commands, use:

    codex plugin marketplace list --json
    codex plugin list --json

Confirm options with command help before use. If the same plugin is already installed and enabled from the correct source, reuse it. If a same-named marketplace points elsewhere, explain the collision rather than replacing it. Preserve all unrelated plugins, MCP registrations, and credentials.

## 2. Add the marketplace and install the plugin

When supported, add the GitHub source with no sparse-path restriction:

    codex plugin marketplace add figurelabs-ai/figurelabs-plugins --ref main

Adding the source is not the same as installing the plugin. If the installed CLI explicitly supports `codex plugin add`, confirm its syntax with help, then install:

    codex plugin add figurelabs@figurelabs-marketplace

Otherwise use the client's documented plugin-install capability. For manual desktop installation: open Plugins, add the repository URL as a marketplace, set Git ref to main, leave sparse paths empty, and install FigureLabs from figurelabs-marketplace. Ask the user to complete only actions the agent cannot perform or that require the user's confirmation.

Verify the installed plugin identity, enabled state, and version against the manifest actually fetched from the selected Git ref. Do not hard-code a release version. Inspect the bundled MCP configuration and verify the production URL above. Do not merely clone the repository and report installation. Do not copy files into the client's cache or create a duplicate standalone MCP registration to bypass installation.

## 3. Connect, authenticate, and verify before creating the dedicated task

Complete the client's supported FigureLabs OAuth/connection flow. Let the user complete interactive sign-in and authorization as required. Never ask the user to paste a password, token, or API key into the conversation. Do not assume a standalone `codex mcp login figurelabs` command addresses a plugin-scoped server; use it only if the host documents that mapping.

Use the host's documented Connect, Authorize, or Reconnect operation for this installed plugin when available. If it presents an authorization URL or opens a login page, guide the user through that flow and keep the installation pending. After the user completes authorization, resume in this installation conversation and recheck the connection. A login page opening, a user saying "done", or a plugin policy of ON_INSTALL is not sufficient evidence of authentication success.

Before step 4, require evidence of BOTH:

- Authentication succeeded for this plugin-scoped test connection, from the host's successful authorization result or current connection status. Do not read or expose credential storage to establish this.
- The host completed MCP initialization/tool discovery for the same connection, for example a successful authenticated tools/list or a host runtime status that confirms discovered tools. Installed skill files and a configured URL do not meet this requirement.

Verify availability without creating a project or generating an image. Use host connection management or read-only discovery; do not invoke figurelabs_project_bootstrap as an authentication probe.

If the host lacks an operation to start authorization, give the exact supported manual connection step and keep this installation pending here. If a restart is required, explain it and resume verification after the user returns. Do not create a dedicated onboarding task merely to move an unresolved authentication or connection problem elsewhere. If the host cannot verify runtime discovery until a new task exists, explain that limitation and ask whether the user wants a diagnostic task; this is an explicit exception, not an automatic substitute for the requested authenticate-first flow.

Missing tools alone do not prove missing authorization. Distinguish an unloaded plugin snapshot, disabled server, discovery/transport error, and a confirmed authentication challenge using observed evidence. Do not repeatedly start OAuth when the host reports valid authentication.

Track installation, authentication, and runtime reachability separately. If any prerequisite remains unknown or failed, report "installed; connection setup pending" with the remaining step. Do not mark the full install-and-start request complete.

## 4. Create exactly one dedicated task when requested

Only if the user's request includes setting up a new task/conversation, use the host's supported new-task capability AFTER installation is verified, the plugin is enabled, AND step 3 has confirmed authentication and runtime discovery. Installation/enabled flags alone must never trigger this step. Prefer a projectless task when supported; installation does not require checking out the user's business repository.

Read the installed plugin's `interface.defaultPrompt` from its `.codex-plugin/plugin.json`. Use the first non-empty string from that array. Do not invent a replacement if the manifest is missing or malformed; report the problem.

Create one task titled "FigureLabs" and send the default prompt as part of its initial message, together with this context:

    This is the dedicated FigureLabs task requested by the user. Stay in this task; do not create another task, even if the default prompt mentions a dedicated task. Use only the installed figurelabs plugin and its MCP connection at https://api.figurelabs.ai/plot-mcp. Verify that its skills and tools are loaded. Follow the plugin's onboarding workflow to connect, create an empty illustration project, open it if supported, and introduce the capabilities in the user's language. Do not generate a sample image. If authorization or a required capability is missing, explain the limitation and guide the user through the supported next step. Do not switch to another FigureLabs environment.

Append the actual default prompt read from the installed manifest. Use one initial message or one follow-up, not both. The child task must not repeat installation or spawn another dedicated task.

Include a brief, non-secret summary of the authentication and discovery evidence from step 3. The new task should reuse the existing authorized connection and check its own loaded tools. It must not request another login unless the host reports that credentials have expired or are invalid. Never copy tokens or credentials into its prompt.

If the host cannot create a task or send its initial prompt, provide the default prompt and ask the user to open a new conversation manually. Do not simulate a new task by relabeling the current conversation or claim a task was created without a returned task identifier.

## 5. Verify the handoff

When host tools support it, open the new task and read its status. Even after preflight succeeds, a created task is not proof that its own plugin snapshot loaded correctly. Let that task verify its own plugin/tool availability and perform onboarding. If it lacks tools, report an unresolved runtime handoff, not a successful end-to-end setup or an assumed OAuth failure. The expected outcome is an empty editable project and an introduction, not a generated figure.

Report only what was observed: installation state and version, authorization/runtime state if checked, the new task link or identifier, and any remaining user action. Do not report connection or project creation before it succeeds. Do not publish, push Git changes, submit content elsewhere, or start paid generation as part of installation.

## Troubleshooting

- "marketplace root does not contain a supported manifest": verify .agents/plugins/marketplace.json exists at repository root on the selected ref, and leave sparse paths empty.
- Plugin missing after adding marketplace: verify the entry points to ./plugins/figurelabs and install the entry explicitly.
- Skills or tools missing in the current task: verify installed/enabled state and host connection/authentication/discovery status; refresh/restart if required. Keep the connection setup pending in the installation conversation until step 3 passes. Creating a diagnostic task requires the explicit exception described there. Do not substitute another environment.
- OAuth or service error: report the actual error; do not claim the production service is online merely because its URL is configured.

Official packaging reference: https://developers.openai.com/plugins/build/plugins
