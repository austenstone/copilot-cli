# GitHub Copilot CLI Action 🤖

[![Fake CI](https://github.com/austenstone/copilot-cli/actions/workflows/ci.yml/badge.svg)](https://github.com/austenstone/copilot-cli/actions/workflows/ci.yml)

A GitHub Action wrapper for the [GitHub Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli) that enables AI-powered automation in your workflow files.

## Installation

### Permissions

Add the `copilot-requests: write` permission to your workflow. The default `GITHUB_TOKEN` now handles Copilot authentication — **no PAT required**.

> [!NOTE]
> Your organization must have the **"Allow use of Copilot CLI billed to the organization"** policy enabled.

### Basic Setup

Add the following workflow to your `.github/workflows` folder:

```yaml
name: 'Copilot Automation'
on: [pull_request]

permissions:
  copilot-requests: write
  pull-requests: write

jobs:
  copilot:
    runs-on: ubuntu-latest
    steps:
      - name: 'Checkout Repository'
        uses: actions/checkout@v5

      - name: 'Run Copilot CLI'
        uses: austenstone/copilot-cli@v3
        with:
          prompt: |
            Review this pull request for:
            1. Code quality and best practices
            2. Security vulnerabilities
            3. Performance implications
            4. Documentation completeness
```

### Advanced Setup with MCP Servers

```yaml
          prompt: 'What time is it?'
          mcp-config: |
            {
              "mcpServers": {
                "time": {
                  "type": "local",
                  "command": "uvx",
                  "args": ["mcp-server-time", "--local-timezone", "America/New_York"],
                  "tools": ["*"]
                }
              }
            }
```

## Configuration

### Input Parameters

| Parameter | Description | Required | Default |
|-----------|-------------|----------|---------|
| `prompt` | Natural language prompt to send to GitHub Copilot | ✅ | - |
| `copilot-token` | *(Deprecated)* Override token for Copilot auth. The default `github.token` now works. | ❌ | `github.token` |
| `repo-token` | Token for repository operations (`gh` CLI). Use a PAT if the agent needs elevated permissions. | ❌ | `github.token` |
| `copilot-config` | GitHub Copilot CLI configuration (JSON) | ❌ | See below |
| `mcp-config` | MCP server configuration in JSON format | ❌ | - |
| **Agent Behavior** | | | |
| `autopilot` | Enable autopilot continuation in prompt mode | ❌ | `true` |
| `max-turns` | Maximum number of autopilot continuation turns | ❌ | unlimited |
| `no-ask-user` | Disable ask_user tool for fully autonomous CI execution | ❌ | `true` |
| `silent` | Output only the agent response without usage statistics | ❌ | `false` |
| `model` | AI model to use (e.g., `"claude-sonnet-4"`, `"gpt-5"`) | ❌ | - |
| `agent` | Specify a custom agent to use (e.g., `"explore"`) | ❌ | - |
| `reasoning-effort` | Reasoning effort level (`low`, `medium`, `high`, `xhigh`) | ❌ | - |
| `experimental` | Enable experimental CLI features | ❌ | `false` |
| **Tool Permissions** | | | |
| `allow-all-tools` | Allow all tools without approval | ❌ | `true` |
| `allowed-tools` | Comma-separated list of tools to allow (e.g., `"shell(git:*)"`) | ❌ | - |
| `denied-tools` | Comma-separated list of tools to deny (e.g., `"shell(rm)"`) | ❌ | - |
| `allowed-urls` | Comma-separated list of URLs/domains to allow | ❌ | - |
| `denied-urls` | Comma-separated list of URLs/domains to deny | ❌ | - |
| **MCP Configuration** | | | |
| `enable-all-github-mcp-tools` | Enable all GitHub MCP tools | ❌ | `false` |
| `add-github-mcp-tools` | Comma-separated list of specific GitHub MCP tools to enable | ❌ | - |
| `add-github-mcp-toolsets` | Comma-separated list of GitHub MCP toolsets to enable | ❌ | - |
| `disable-mcp-servers` | Comma-separated list of MCP servers to disable | ❌ | - |
| `disable-builtin-mcps` | Disable all built-in MCP servers | ❌ | `false` |
| **Files & Directories** | | | |
| `additional-directories` | Comma-separated list of additional directories to trust | ❌ | - |
| **Session Management** | | | |
| `resume-session` | Resume from a previous session ID (use `"latest"` for most recent) | ❌ | - |
| `share` | Share session to a markdown file after completion | ❌ | - |
| `share-gist` | Share session to a secret GitHub gist | ❌ | `false` |
| **Output & Logging** | | | |
| `output-format` | Output format (`json` for JSONL output) | ❌ | - |
| `log-level` | Log level: `none`, `error`, `warning`, `info`, `debug`, `all` | ❌ | `all` |
| `upload-artifact` | Upload Copilot logs as workflow artifacts | ❌ | `true` |
| `fail-on-error` | Fail the step if Copilot CLI exits with non-zero code | ❌ | `false` |
| `copilot-version` | Version of Copilot CLI to install | ❌ | `prerelease` |
| `options` | Additional CLI flags (e.g., `"--no-custom-instructions"`) | ❌ | `--screen-reader --no-color --stream off` |

### Output Parameters

| Output | Description |
|--------|-------------|
| `exit-code` | Exit code from the Copilot CLI command |
| `logs-path` | Path to the copilot logs directory |
| `session-path` | Path to the shared session markdown file (when `share` is used) |

### MCP Server Configuration

The action supports Model Context Protocol (MCP) servers for extending Copilot's capabilities. Configure MCP servers using JSON format with an `mcpServers` object where each key is the server name and the value contains its configuration.

> [!IMPORTANT]
> See the [official MCP server configuration docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/extend-coding-agent-with-mcp#writing-a-json-configuration-for-mcp-servers) for complete details.

## Examples

<details>
<summary>📋 <strong>View All Example Workflows</strong></summary>

| Workflow | Description |
|----------|-------------|
| [CI Fix](.github/workflows/copilot-ci-fix.yml) | Automatically analyzes failed workflow runs and creates a pull request with fixes |
| [Comment Trigger](.github/workflows/copilot-comment.yml) | Responds to issue comments starting with `/copilot` and executes the requested task |
| [Dependabot Analysis](.github/workflows/copilot-dependabot-update.yml) | Reviews Dependabot PRs with detailed dependency analysis, breaking changes, and migration guidance |
| [PR Review](.github/workflows/copilot-pr-review.yml) | Performs comprehensive autonomous code reviews on pull requests with severity-based feedback |
| [Research](.github/workflows/copilot-research.yml) | Conducts deep research on GitHub issues using Firecrawl to gather and synthesize information |
| [Security Triage](.github/workflows/copilot-security-triage.yml) | Triages all security alerts (Dependabot, Secret Scanning, Code Scanning) into a single comprehensive report |
| [Issue Triage](.github/workflows/copilot-triage.yml) | Automatically labels issues based on their title and content using existing repository labels |
| [Usage Report](.github/workflows/copilot-usage-report.yml) | Generates comprehensive Copilot usage reports and analytics |

</details>

## Troubleshooting

> [!NOTE]
> Most issues stem from permissions configuration.

### Common Issues

1. **"Copilot token required" / Permission Denied**
   - Ensure your workflow has `copilot-requests: write` permission
   - Your org must enable the **"Allow use of Copilot CLI billed to the organization"** policy
   - If using a legacy PAT, ensure it has the "Copilot Requests" permission

2. **Copilot starts but permission denied on repo operations**
   - Add appropriate permissions (e.g., `contents: write`, `pull-requests: write`)
   - Check Settings > Actions > General > Workflow permissions

3. **Tool Access Denied**
   - Check your `allowed-tools` and `denied-tools` configuration
   - If `allow-all-tools: false`, you must explicitly allow needed tools

4. **MCP Server Connection Issues**
   - Verify MCP server URLs are accessible from GitHub-hosted runners
   - Check authentication headers and tokens
   - Ensure `type` is set correctly (`local`, `http`, or `sse`)

5. **Session Resume Not Working**
   - Session data is stored in logs; ensure `upload-artifact: true`
   - Use `resume-session: latest` to continue the most recent session

6. **Large Output Truncation**
   - Set `log-level: error` or `log-level: warning` to reduce verbosity
   - Break complex prompts into smaller, focused tasks

## Related Resources

- [GitHub Copilot CLI Documentation](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/)
- [MCP Server Configuration Docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/extend-coding-agent-with-mcp#writing-a-json-configuration-for-mcp-servers)
- [Search for workflow examples](https://github.com/search?q=%22copilot+-p%22+path%3A.github%2Fworkflows%2F*.yml&ref=opensearch&type=code)

