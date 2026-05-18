# github-mcp

A Claude Desktop extension that connects Claude to GitHub's MCP API at
`https://api.githubcopilot.com/mcp` via Streamable HTTP.

Provides tools for repositories, issues, pull requests, code search, and
more — without any local server to install or maintain.

## Install

### As a Claude Desktop Extension (one-click)

Grab the latest `github-mcp.mcpb` from the
[Releases](https://github.com/kyletaylored/github-mcp-server-mcpb/releases)
page and double-click it. Claude Desktop's Extensions UI will ask for:

1. **GitHub Personal Access Token** — paste a PAT with the scopes listed
   below. The value is stored encrypted by Claude Desktop; you can update
   it later in the extension settings.
2. **Install** — confirms and registers the GitHub MCP tools.

Requires `node` / `npx` on your `PATH` — installed with
[Node.js](https://nodejs.org/) or `brew install node` on macOS.

## GitHub Personal Access Token

Generate a token at <https://github.com/settings/tokens>.

### Recommended scopes

| Scope | Why |
|---|---|
| `repo` | Full repository access (read + write) |
| `read:user` | Read your GitHub profile |
| `read:org` | Read organization membership |
| `read:packages` | Read GitHub Packages |
| `gist` | Read/write gists |

A classic PAT with `repo` + `read:user` covers most use cases. For
fine-grained PATs, grant **Contents**, **Issues**, **Pull requests**,
**Metadata** (read-only) to the repositories you want to access.

## What it does

Once installed, you can ask Claude things like:

> *"List open pull requests in kyletaylored/github-mcp-server-mcpb."*
> *"Create an issue titled 'Bug: auth fails' in my-org/my-repo."*
> *"Show me the README for the anthropics/claude-code repository."*
> *"Search GitHub for MCP server examples written in TypeScript."*

The full set of available tools is determined by the GitHub Copilot MCP
server — Claude will advertise them after installation.

## Manually (Claude Desktop config)

If you prefer to wire this up by hand instead of using the `.mcpb`:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote@latest",
        "https://api.githubcopilot.com/mcp",
        "--header",
        "Authorization: Bearer YOUR_GITHUB_PAT",
        "--transport",
        "http-only"
      ]
    }
  }
}
```

Drop that (with your real token) into your Claude Desktop config and
restart the app:

| OS | Config path |
|---|---|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |

## Architecture note

The `.mcpb` format packages local MCP servers (Python, Node, binary). The
GitHub Copilot MCP endpoint is a remote Streamable HTTP server, so this
bundle uses [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) as a
thin stdio-to-HTTP bridge. No custom server code is bundled — `mcp-remote`
is pulled at runtime via `npx`.

## Releasing

The CI workflow builds and attaches a `.mcpb` to any `v*` tag push:

```bash
git tag v1.0.0
git push --tags
```

Bump the `version` field in `manifest.json` to match each new tag before
pushing.

## License

MIT — see [LICENSE](LICENSE).
