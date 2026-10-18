# Searcherries plugin for Codex

A plugin marketplace for the Codex CLI with one plugin, `searcherries`: the read-only Searcherries MCP server plus eight GEO skills that turn its data into page changes.

[Searcherries](https://searcherries.com) tracks how AI assistants answer the questions your customers ask, which brands and sources those answers name, and what your Google Search Console, Bing Webmaster Tools and GA4 AI-traffic data says. This plugin connects Codex to that data and adds ready-made workflows for GEO work. When Codex works in your website's repository, the workflows can end with edits to the site's code, shown to you before they are applied.

## Install

In a terminal with the Codex CLI installed:

```sh
codex plugin marketplace add eduardmur/searcherries-codex-plugin
codex plugin add searcherries@searcherries
codex mcp login searcherries
```

The first command registers this repository as a marketplace named `searcherries`. The second installs the plugin from it (Codex requires the `plugin@marketplace` form, or `codex plugin add searcherries --marketplace searcherries`). The third opens the Searcherries sign-in in your browser; sign in with the account whose projects Codex should read.

Then start a new Codex session. Bundled skills and MCP servers become available only in sessions started after the install. `/mcp` shows the connection, `/plugins` opens the plugin browser, and `$` followed by a skill name invokes a workflow, for example `$action-plan`.

Full guide: [searcherries.com/docs/mcp/codex](https://searcherries.com/docs/mcp/codex).

Codex in the ChatGPT desktop app installs plugins from the shared plugin directory instead, where Searcherries is [listed](https://chatgpt.com/plugins/plugin_asdk_app_6abffa47a734819196515adc0ee91212). This repository exists for the CLI, whose `/plugins` browser and `codex plugin add` install only from configured marketplaces.

## What's included

| Component | What it does |
|-----------|--------------|
| MCP server `searcherries` | `https://app.searcherries.com/mcp/searcherries`: read-only, OAuth sign-in, nine tools for AI visibility, competitors, citations, Search Console, Bing and GA4 data |
| `$action-plan` | Your next five priorities with dated evidence, an owner and a success measure (`project`, `audience`, `period`) |
| `$geo-audit` | Visibility score and change, where the brand is invisible, who wins, which sources shape the answers, five actions (`project`, `period`) |
| `$content-gaps` | Pages to publish or update, with the demand that proves each one (`project`, `topic`) |
| `$competitor-gap` | One competitor: the questions it wins, what it is praised for, the pages that cite it, a counter-content plan (`competitor`, `project`) |
| `$page-brief` | A brief for one page: queries and AI questions to answer, citation competition, sections and facts (`url`, `project`, `question`) |
| `$question-review` | Which tracked questions to keep, rephrase, retire or add (`project`) |
| `$weekly-report` | One-page status with deltas, trends, competitor moves and three actions (`project`, `period`) |
| `improve-ai-visibility` | Applied when you ask why AI assistants leave your brand out or what to change on your site: gaps, competitors, citations, search demand, then page changes |

Every workflow reads data through the MCP server only. Nothing in this plugin runs scripts, stores data or sends data anywhere other than the Searcherries server you sign in to.

## Requirements

A Searcherries account with at least one project. The free plan includes the Google Search Console and GA4 data you connect, so connect at least one of them, or there is nothing to read. AI visibility tracking (how AI assistants answer your customers' questions, which competitors and sources they name) and Bing Webmaster Tools need a paid plan, Starter or Pro (an active trial counts); on the free plan the action-plan workflow and questions about search and traffic still work. [Compare plans](https://searcherries.com/pricing) or [start here](https://app.searcherries.com/register). Codex CLI 0.163 or newer; the install was verified with 0.163.0 on 2026-10-17.

## Try it

- `$action-plan` and "What should I work on next?"
- `$competitor-gap Acme` to see why Acme is recommended instead of you and how to catch up
- "Which of our pages should we update first to get recommended by AI assistants? Show the evidence." runs the `improve-ai-visibility` skill

Every number comes with the period it was measured in. The server is read-only: it cannot change projects, questions or billing, and it only sees the projects of the account you authorize. Tool reference: [searcherries.com/docs/mcp/tools](https://searcherries.com/docs/mcp/tools).

## Repository layout

```text
.agents/plugins/marketplace.json   the marketplace catalog
plugins/searcherries/
├── plugin.json                    Agent Plugins manifest with the OpenAI interface metadata
├── mcp.json                       the streamable-http MCP server
├── assets/logo.svg
└── skills/<name>/SKILL.md         the eight skills
```

The layout follows OpenAI's guide [Package your plugin](https://developers.openai.com/plugins/build/plugins) (read on 2026-10-17), in particular:

- "Marketplace metadata" and "Install a local plugin manually": the catalog lives at `$REPO_ROOT/.agents/plugins/marketplace.json` with a top-level `name`, `interface.displayName` and one object per plugin under `plugins[]`, each with `name`, a `source` of `{"source": "local", "path": "./..."}` relative to the marketplace root, `policy.installation`, `policy.authentication` and `category`.
- "Plugin structure" and "Path rules": the plugin keeps `plugin.json`, `mcp.json`, `skills/` and `assets/` at its root, declares the Agent Plugins schemas `https://agent-plugins.org/schemas/1.0.0/plugin.schema.json` and `https://agent-plugins.org/schemas/1.0.0/mcp.schema.json`, discovers skills from `skills/<skill-name>/SKILL.md` without a `skills` field, and puts OpenAI-specific presentation under `extensions.com.openai.interface`.
- "Add a marketplace from the CLI": `codex plugin marketplace add` accepts GitHub shorthand (`owner/repo` or `owner/repo@ref`), Git URLs and local marketplace roots.

The plugin browser itself is described in [Plugins](https://learn.chatgpt.com/docs/plugins?surface=cli) under "Plugin browser in Codex CLI", and the command reference in [Developer commands](https://learn.chatgpt.com/docs/developer-commands) under `codex plugin` and `codex plugin marketplace`.

## Develop and test locally

```sh
git clone https://github.com/eduardmur/searcherries-codex-plugin.git
codex plugin marketplace add ./searcherries-codex-plugin
codex plugin add searcherries@searcherries
```

Codex copies the plugin into `~/.codex/plugins/cache/searcherries/searcherries/<version>/`. After a change, bump `version` in `plugins/searcherries/plugin.json`, then run `codex plugin marketplace upgrade searcherries` (Git marketplaces) or remove and add the plugin again (local marketplaces).

The seven workflow skills mirror the prompts of the Searcherries MCP server one to one; when a server prompt changes, the matching `SKILL.md` changes with it.

## Support

[Open an issue](https://github.com/eduardmur/searcherries-codex-plugin/issues) or write from your account at [app.searcherries.com](https://app.searcherries.com).

## License

MIT
