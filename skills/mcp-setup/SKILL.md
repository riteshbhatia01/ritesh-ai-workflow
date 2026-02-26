---
name: mcp-setup
description: Interactive guide to set up MCP (Model Context Protocol) servers for Claude Code or Cursor. Use when the user wants to connect AI tools to external services like Slack, Figma, Jira, GitHub, or Grafana.
disable-model-invocation: true
allowed-tools:
  - Bash
  - Read
  - Write
---

# MCP Setup Guide

Interactive guide to connect MCP servers to your AI coding tool.

## Step 1: Identify the Tool

Ask: **Which AI tool are you setting up?**
- Claude Code
- Cursor
- Both

## Step 2: Choose Servers

Present available MCP servers:

| Server | What it does | Auth |
|--------|-------------|------|
| **GitHub** | PRs, issues, CI, code search | OAuth |
| **Slack** | Channels, threads, canvases, search | OAuth |
| **Figma** | Designs, components, screenshots | Built-in |
| **Jira** | Issues, JQL search, projects | API token |
| **Grafana** | Dashboards, alerts, metrics | Service account |
| **PostgreSQL** | Database queries, schema exploration | Connection string |

Full registry: https://registry.modelcontextprotocol.io/

Ask which servers the user wants to set up.

## Step 3: Generate Setup

### Claude Code

For each selected server, provide the exact command:

**GitHub:**
```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
```

**Slack:**
```bash
claude mcp add --transport http slack https://mcp.slack.com/mcp
```

**Jira:**
```bash
claude mcp add --transport http jira https://mcp.atlassian.com/v2/mcp
```

**Grafana:**
```bash
claude mcp add --transport stdio grafana -- uvx mcp-grafana
# Then set GRAFANA_URL and GRAFANA_SERVICE_ACCOUNT_TOKEN
```

**Figma:**
Follow setup at https://developers.figma.com/docs/figma-mcp-server/

### Cursor

Guide through: Settings > MCP > Add Server
- HTTP servers: provide the URL
- stdio servers: provide command and args

## Step 4: Verify

```bash
claude mcp list
```

Test each server with a simple command:
- GitHub: "List my open PRs"
- Slack: "Read the last 5 messages in #general"
- Jira: "Search for my open tickets"

## Step 5: Permissions

Explain permission options:
- Claude Code prompts on first use of each tool
- Pre-approve in `settings.json`: `"mcp__slack__*"` allows all Slack tools
- Use specific permissions for sensitive tools

## Reference Links

- MCP Protocol: https://modelcontextprotocol.io/introduction
- Claude Code MCP: https://code.claude.com/docs/en/mcp
- Cursor MCP: https://cursor.com/docs/context/mcp
- Server Registry: https://registry.modelcontextprotocol.io/
- GitHub MCP: https://github.com/github/github-mcp-server
- Grafana MCP: https://github.com/grafana/mcp-grafana
- Figma MCP: https://developers.figma.com/docs/figma-mcp-server/
