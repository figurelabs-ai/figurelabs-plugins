# FigureLabs plugin

The official FigureLabs plugin for OpenAI Codex and Claude Code. It lets an AI assistant design scientific figures and diagrams in [FigureLabs](https://www.figurelabs.ai): mechanism illustrations, graphical abstracts, data plots from your own files, and flowcharts. Every figure stays editable in a FigureLabs project, can be changed in plain language as a new version, and can be split into layers.

The plugin connects to the FigureLabs MCP server at `https://mcp.figurelabs.ai/mcp`. You need a FigureLabs account.

AI agents installing the plugin on a user's behalf: follow [AGENTS.md](AGENTS.md).

## What the plugin contains, runs, sends and fetches

- **Contents:** six skills (Markdown instructions for the assistant), one MCP server entry, and icons. There are no scripts, hooks, binaries or local servers; installing the plugin runs nothing on your machine.
- **Connection:** the assistant connects to the remote FigureLabs MCP server listed in `plugins/figurelabs/.mcp.json`. You sign in with your FigureLabs account through OAuth in your browser; the plugin never asks for or stores passwords or API keys.
- **What is sent to FigureLabs:** the figure descriptions the assistant writes from your conversation, edit instructions, and only the files you explicitly ask it to use (for example a CSV for a plot, or a sketch or reference image). A local file is uploaded only when you ask for it.
- **What is fetched:** job status, figure previews, layers of a split, your credit balance and links to your FigureLabs projects.
- **Credits:** creating, editing or splitting a figure uses credits from your FigureLabs plan. Viewing figures, checking a job, checking your balance, opening a project and importing files do not.

See the FigureLabs [privacy policy](https://www.figurelabs.ai/privacy) and [terms](https://www.figurelabs.ai/terms).

## Install in Codex

Use a Codex version with plugin marketplace support.

```sh
codex plugin marketplace add figurelabs-ai/figurelabs-plugins --ref main
codex plugin add figurelabs@figurelabs-marketplace
```

Or, in the Codex app, open **Plugins**, find the **FigureLabs** marketplace and install **FigureLabs**. Complete the sign-in when prompted, then start a new conversation.

## Install in Claude Code

In a Claude Code session:

```text
/plugin marketplace add figurelabs-ai/figurelabs-plugins
/plugin install figurelabs@figurelabs
```

Then run `/mcp`, select **figurelabs** and authenticate in the browser. From a terminal the same steps are `claude plugin marketplace add figurelabs-ai/figurelabs-plugins` and `claude plugin install figurelabs@figurelabs`.

## Install from a local clone

```sh
git clone https://github.com/figurelabs-ai/figurelabs-plugins.git
codex plugin marketplace add ./figurelabs-plugins     # Codex
claude plugin marketplace add ./figurelabs-plugins    # Claude Code
```

Then install the plugin as above. Use either the GitHub source or the local one, so you know which copy is installed.

## Update

```sh
# Codex
codex plugin marketplace upgrade figurelabs-marketplace

# Claude Code
claude plugin marketplace update figurelabs
claude plugin update figurelabs@figurelabs
```

Then start a new conversation; running sessions keep the copy they loaded.

**Coming from 0.3.x or 0.4.x:** version 0.5.0 connects to a new address, so sign in to FigureLabs once more after updating. Skills and tools were renamed; if the assistant still refers to old names, the old copy is cached: update again and open a new conversation.

## Try it

- "Turn this paper abstract into a graphical abstract that highlights the key finding." (paste an abstract)
- "Plot the attached CSV as a bar chart with error bars, one bar per treatment group."
- "Draw a flowchart of the experimental procedure described in my Methods section."
- "Turn this hand sketch into a clean schematic." (with the sketch attached or as a local file)
- "Make the arrows red and rename the second panel to 'Treatment'." (after a figure is made)
- "Split this figure into layers so I can move the elements around in my slides." (after a figure is made, or with an image)
- "How many FigureLabs credits do I have left?"

A figure usually takes one to a few minutes, a layer split two to five. In Codex and Claude Code the assistant waits for the result and gives you the link to the FigureLabs project.

## Skills

| Skill | Purpose |
| --- | --- |
| `figurelabs-onboarding` | Confirm the connection, introduce capabilities, check credits and the space in use, open an empty project on request |
| `figurelabs-generation` | Choose illustration, plot or flowchart; write a detailed prompt from the conversation; set style, ratio and chart type; tell the user about credits |
| `figurelabs-edit` | Change an existing figure as a new version, optionally with reference images |
| `figurelabs-layers` | Split a figure or image into layers (transparent PNGs with positions) and its text |
| `figurelabs-file-import` | Bring in data files and reference images the user provides |
| `figurelabs-delivery` | Wait for the job, present the result, handle errors |

## Tools

The MCP server provides these tools; the skills tell the assistant when to use each.

| Tool | What it does | Credits |
| --- | --- | --- |
| `figurelabs_create_illustration` | Mechanism diagram, schematic or graphical abstract from a description, optionally from a sketch or reference image | yes |
| `figurelabs_create_plot` | Chart (bar, line, scatter, box, violin, heatmap and more) from your data | yes |
| `figurelabs_create_flowchart` | Process diagram with steps, branches and loops | yes |
| `figurelabs_edit_figure` | Change a figure in plain language; saved as a new version | yes |
| `figurelabs_split_layers` | Split an illustration or an image into layers and recognized text | yes, per layer |
| `figurelabs_get_job` | Status and result of a create, edit or split job | no |
| `figurelabs_show_figure` | Show an existing figure again | no |
| `figurelabs_get_credits` | Credit balance, plan, and whether the connection works in your team or personal space | no |
| `figurelabs_open_project` | Create an empty project or reopen one | no |
| `figurelabs_upload_file` | Import a file from a link or from the conversation | no |
| `figurelabs_create_upload_session` | One-time upload for a local file | no |

## Good to know

- **Team and personal spaces:** a connection works in one space. Team and personal spaces share neither credits nor figures; ask the assistant which space is in use if a figure or your credits are not where you expect them.
- **Data plots use only your data.** FigureLabs does not invent measurements or statistics.
- **Not supported:** photographs of people, and imitations of experimental images such as gels, blots or micrographs.
- **Files:** up to 20 MB each. Plot data as CSV, TSV, JSON, XLS or XLSX; references as images or PDF.
- **Disconnect:** remove or sign out of the FigureLabs server in the host's plugin or MCP settings, or uninstall the plugin (`codex plugin remove figurelabs@figurelabs-marketplace`, `claude plugin uninstall figurelabs@figurelabs`).

## Layout

```text
AGENTS.md                            installation guide for AI agents
.agents/plugins/marketplace.json     Codex marketplace
.claude-plugin/marketplace.json      Claude Code marketplace
plugins/figurelabs/
  .codex-plugin/plugin.json          Codex manifest and listing metadata
  .claude-plugin/plugin.json         Claude Code manifest
  .mcp.json                          remote MCP server (shared)
  skills/                            shared skills
  assets/                            icons
```

## Support

support@figurelabs.ai

## License

MIT. See [LICENSE](LICENSE).
