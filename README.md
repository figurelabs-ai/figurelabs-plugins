# FigureLabs Plugin

FigureLabs plugin with MCP integration and scientific visualization skills for creating and editing scientific illustrations, data plots, and flowcharts in Codex.

This repository distributes the **production environment** plugin. It connects to `https://api.figurelabs.ai/plot-mcp` and requires a FigureLabs account with access to FigureLabs. The remote service runs separately from this repository; downloading the files does not start or deploy a server.

## Install in Codex

Use a Codex version that supports plugin marketplaces:

```sh
codex plugin marketplace add figurelabs-ai/figurelabs-plugins --ref main
```

Open the client's Plugins page, find the **FigureLabs Marketplace** source, and install **FigureLabs**. Complete the authentication flow when prompted, then start a new conversation and select the plugin. The marketplace identifier is `figurelabs-marketplace`.

If the source does not appear, restart the client and inspect registered sources with:

```sh
codex plugin marketplace list
```

If your client does not recognize `codex plugin marketplace`, update to a version with plugin marketplace support. Support for third-party marketplaces varies by client and workspace policy. Publishing this repository does not list the plugin in OpenAI's public Plugins Directory.

### Install from a local clone

```sh
git clone https://github.com/figurelabs-ai/figurelabs-plugins.git
cd figurelabs-plugins
codex plugin marketplace add .
```

Then install and authenticate the plugin in the client as above. Use either the GitHub source or the local source for a production session, so you can identify which copy is installed.

## Try it

Start with an empty project to check connection and authorization:

> Connect to FigureLabs, open an empty illustration project, and introduce the available capabilities. Do not generate an image yet.

Then try a workflow:

- Create a scientific illustration explaining a research mechanism.
- Create a data plot from an attached CSV, XLS, or XLSX file.
- Turn an experimental procedure into an editable flowchart.
- Change the labels, colors, or layout of an existing figure.

Generation and editing use the production service's account permissions, quotas, and applicable credit rules. Browser interactions require a compatible browser capability in the host; the skills describe a fallback when it is unavailable.

## Included workflows

| Skill | Purpose |
| --- | --- |
| `figurelabs-onboarding` | Connect, introduce capabilities, and create or reopen a workspace |
| `figurelabs-generation` | Generate scientific illustrations, real-data plots, and flowcharts |
| `figurelabs-browser-handoff` | Open or refresh the editable project and handle browser limitations |
| `figurelabs-canvas-edit` | Revise an existing figure or use available canvas controls |
| `figurelabs-file-import` | Import reference images and plotting data |

## Repository layout

```text
.agents/plugins/marketplace.json
plugins/figurelabs/
  .codex-plugin/plugin.json
  .mcp.json
  skills/
README.md
README.zh-CN.md
.gitignore
```

The marketplace points to `./plugins/figurelabs`, relative to the repository root. Keep all skill reference files and the dot-prefixed configuration files when copying or uploading the repository.

## Update

```sh
codex plugin marketplace upgrade figurelabs-marketplace
```

Refresh or update the installed plugin in the client as needed, then start a new conversation. Installed plugins use a cached copy; changing the repository does not change every existing session immediately.

## Maintainer upload

Push the **contents of this directory** to the root of `figurelabs-ai/figurelabs-plugins` on `main`. The repository root must contain `.agents/` and `plugins/`, without an extra enclosing folder. Do not upload a ZIP as the only repository file: Codex needs the extracted files in Git.

Only the client plugin configuration and skills are included. Each user authenticates separately; do not commit access tokens, API keys, local client settings, or backend configuration. Keep the `figurelabs` name and production endpoint together. Distribute a separately named production plugin when you are ready for production use.

See the [official OpenAI plugin packaging documentation](https://developers.openai.com/plugins/build/plugins) for marketplace setup details.
