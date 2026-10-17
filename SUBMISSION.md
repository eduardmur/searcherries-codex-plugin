# Publishing checklist

What was built on 2026-10-17, what was verified, and what remains to be done by hand.

## State of this repository

- Published at https://github.com/eduardmur/searcherries-codex-plugin on 2026-10-17 (default branch `master`). The GitHub shorthand was verified the same day with Codex CLI 0.163.0: `codex plugin marketplace add eduardmur/searcherries-codex-plugin` cloned it into `~/.codex/.tmp/marketplaces/searcherries`, `codex plugin add searcherries@searcherries` installed 1.0.0, and `codex mcp list` shows the server as enabled, not logged in.
- Verified with Codex CLI 0.163.0 (installed with `npm i -g @openai/codex`; the ChatGPT desktop app also bundles a CLI at `/Applications/ChatGPT.app/Contents/Resources/codex-cli/bin/codex`, 0.162.0-alpha.17.2, with the same plugin commands):
  - `codex plugin marketplace add ./searcherries-codex-plugin` registers the marketplace `searcherries` (name taken from `.agents/plugins/marketplace.json`).
  - `codex plugin list --available --json` lists `searcherries@searcherries` 1.0.0 with `installPolicy: AVAILABLE`, `authPolicy: ON_INSTALL`.
  - `codex plugin add searcherries` alone fails ("plugin requires --marketplace unless passed as <plugin>@<marketplace>"); `codex plugin add searcherries@searcherries` installs into `~/.codex/plugins/cache/searcherries/searcherries/1.0.0/` and writes `[plugins."searcherries@searcherries"] enabled = true` to `~/.codex/config.toml`.
  - `codex mcp list` shows `searcherries https://app.searcherries.com/mcp/searcherries enabled Not logged in`, so the CLI recognises the bundled server as OAuth-protected without any `extensions.com.openai.auth` block in `mcp.json`.
  - In a Codex session, `/mcp` prints `searcherries: authentication required (0 tools)` and the startup warning reads "The searcherries MCP server is not logged in. Run `codex mcp login searcherries`." The `$` picker lists the skills as `action-plan (searcherries)`, `question-review (searcherries)` and so on.
- The Git route was verified without GitHub: the committed repository was served by a local HTTP Git server and `codex plugin marketplace add http://127.0.0.1:8765/searcherries-codex-plugin.git` cloned it into `~/.codex/.tmp/marketplaces/searcherries`, read `.agents/plugins/marketplace.json` there, installed `searcherries@searcherries` from the clone and answered `codex plugin marketplace upgrade searcherries`. `file://` URLs are rejected ("expected owner/repo, a git URL, or a local marketplace path").
- Not verified: the OAuth sign-in (`codex mcp login searcherries`) and tool calls after it, which need the account owner; the Plugins Directory of the ChatGPT desktop app, which does not read CLI marketplaces.
- On this Mac the marketplace is now registered from GitHub (the local-path registration was removed on 2026-10-17).

## 1. Create the public repository and push (done 2026-10-17)

```sh
cd ~/Projects/searcherries-codex-plugin
gh repo create eduardmur/searcherries-codex-plugin --public --source . --remote origin --push --description "Searcherries plugin marketplace for the Codex CLI: AI visibility and SEO data for GEO work"
```

Commits are authored as `Searcherries <107179107+eduardmur@users.noreply.github.com>`, like the other plugin repositories.

Then verify the published path from a clean state:

```sh
codex plugin remove searcherries@searcherries
codex plugin marketplace remove searcherries
codex plugin marketplace add eduardmur/searcherries-codex-plugin
codex plugin add searcherries@searcherries
codex mcp login searcherries
```

Start a new `codex` session, run `/mcp` (expect `searcherries: connected (9 tools)`) and try `$action-plan`.

## 2. A finding to decide on: the directory plugin is installable from the CLI

Codex CLI 0.163 exposes the public Plugins Directory as a built-in remote marketplace named `openai-curated-remote`. `codex plugin list --available --json` lists `searcherries@openai-curated-remote` (version 1.0.3, `source.id = plugin_asdk_app_6abffa47a734819196515adc0ee91212`), and `codex plugin add searcherries@openai-curated-remote` installed it into `~/.codex/plugins/cache/openai-curated-remote/searcherries/1.0.3/` (tested on 2026-10-17, then removed again with `codex plugin remove searcherries@openai-curated-remote`).

This corrects the earlier conclusion that the CLI cannot install a directory plugin. Caveats before advertising it: the marketplace name `openai-curated-remote` is not documented anywhere public, and the help page says some directory plugins are unavailable when Codex is signed in with an API key. This repository stays useful either way: it adds the seven workflow skills the directory package does not contain, and it does not depend on an undocumented name.

## 3. Follow-up in app.searcherries.com (done 2026-10-17 on `/mcp#codex` and in the docs of both repositories)

Add the marketplace route next to the existing `codex mcp add` commands:

- `resources/views/profile/mcp.blade.php`, Codex tab (`/mcp#codex`): a disclosure "Install as a plugin from the CLI" with the three commands (`codex plugin marketplace add eduardmur/searcherries-codex-plugin`, `codex plugin add searcherries@searcherries`, `codex mcp login searcherries`). Keep the two `codex mcp` commands first; `McpPageTest` covers the page.
- `docs/public/en/mcp/codex.md`: a section with the same commands, what the plugin contains (server plus eight skills) and that bundled skills appear in sessions started after the install.
- Mirror the page to `~/Projects/searcherries.com/docs/public/en/mcp/codex.md` (the two `en` folders must stay identical, `diff -rq`) and translate into `~/Projects/searcherries.com/docs/public/es/mcp/codex.md`.
- Mention the repository in the MCP section of `CLAUDE.md` and in `docs/MCP.md` next to the Claude, Grok and Cursor plugin repositories.
- Optionally reference `codex plugin add searcherries@openai-curated-remote` as the one-command path to the directory plugin (see section 2).

## 4. Keeping the plugin current

- The seven workflow skills are copies of the Searcherries MCP prompts (`app/Mcp/Prompts/*`), with the sentence about where to connect Searcherries reduced to "tell the user to connect Searcherries first and stop", because listing text for OpenAI surfaces must not name other AI products. `improve-ai-visibility` is the copy from `distribution/openai-plugin/`.
- When a prompt or the directory package changes, update the matching `SKILL.md` here, bump `version` in `plugins/searcherries/plugin.json`, commit and push. Users refresh with `codex plugin marketplace upgrade searcherries`.
- `plugin.json` carries the directory listing's `extensions.com.openai.interface` block; the portal-only `review` and `publication` blocks were left out on purpose.
