# GitHub Copilot CLI Action 🤖

A GitHub Action wrapper for the [GitHub Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli) that enables AI-powered automation in your workflow files.

## Installation

### Permissions

Add the `copilot-requests: write` permission to your workflow. The default `GITHUB_TOKEN` now handles Copilot authentication — **no PAT required**.

> [!IMPORTANT]
> Declaring a `permissions:` block sets every scope you do not list to `none`. Always include `contents: read` if you check out the repo, and `copilot-requests: write` or Copilot auth will fail.

> [!NOTE]
> Your organization must have the **"Allow use of Copilot CLI billed to the organization"** policy enabled.

### Basic Setup

Add the following workflow to your `.github/workflows` folder:

```yaml
name: 'Copilot Automation'
on: [pull_request]

permissions:
  contents: read
  copilot-requests: write
  pull-requests: write

jobs:
  copilot:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    steps:
      - name: 'Checkout Repository'
        uses: actions/checkout@v7

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

### Redacting Secrets in a Large Repo

Use `secret-env-vars` to strip and redact sensitive values from shell/MCP environments and logs, and `context: long_context` to give the agent a larger context window for big codebases:

```yaml
      - name: 'Run Copilot CLI'
        uses: austenstone/copilot-cli@v3
        env:
          API_KEY: ${{ secrets.API_KEY }}
        with:
          prompt: 'Audit the codebase for hard-coded credentials and summarize findings.'
          secret-env-vars: 'API_KEY'
          context: 'long_context'
```

## Configuration

### Input Parameters

| Parameter | Description | Required | Default |
|-----------|-------------|----------|---------|
| `prompt` | Natural language prompt to send to GitHub Copilot | ✅ | - |
| `copilot-token` | *(Deprecated)* Override token for Copilot auth. The default `github.token` now works. | ❌ | `github.token` |
| `repo-token` | Token for repository operations (`gh` CLI). Use a PAT if the agent needs elevated permissions. | ❌ | `github.token` |
| `copilot-config` | Copilot CLI settings (JSON), merged into `~/.copilot/settings.json` (or `config.json` on CLIs older than `1.0.35`) | ❌ | See below |
| `mcp-config` | MCP server configuration in JSON format | ❌ | - |
| **Agent Behavior** | | | |
| `autopilot` | Enable autopilot continuation in prompt mode | ❌ | `true` |
| `max-turns` | Maximum number of autopilot continuation turns | ❌ | CLI default (`5`) |
| `mode` | Initial agent mode (`interactive`, `plan`, or `autopilot`). When set, it overrides the autopilot toggle. | ❌ | - |
| `no-ask-user` | Disable ask_user tool for fully autonomous CI execution | ❌ | `true` |
| `silent` | Output only the agent response without usage statistics | ❌ | `false` |
| `model` | AI model to use (e.g., `"claude-sonnet-4.6"`, `"claude-opus-4.8"`, `"gpt-5.5"`) | ❌ | - |
| `agent` | Specify a custom agent to use (e.g., `"explore"`) | ❌ | - |
| `reasoning-effort` | Reasoning effort level (`none`, `low`, `medium`, `high`, `xhigh`, `max`) | ❌ | - |
| `context` | Context window tier (`default` or `long_context`). Useful for large repos. | ❌ | - |
| `attachments` | Comma-separated file paths (images or native documents) to attach to the prompt | ❌ | - |
| `experimental` | Enable experimental CLI features | ❌ | `false` |
| **Tool Permissions** | | | |
| `allow-all-tools` | Allow all tools without approval | ❌ | `true` |
| `allowed-tools` | Comma-separated list of tools to allow (e.g., `"shell(git:*)"`) | ❌ | - |
| `denied-tools` | Comma-separated list of tools to deny (e.g., `"shell(rm)"`) | ❌ | - |
| `available-tools` | Comma-separated allowlist — only these tools are available to the model | ❌ | - |
| `excluded-tools` | Comma-separated tools to hide from the model | ❌ | - |
| `allowed-urls` | Comma-separated list of URLs/domains to allow | ❌ | - |
| `denied-urls` | Comma-separated list of URLs/domains to deny | ❌ | - |
| `allow-all-urls` | Allow access to all URLs without confirmation | ❌ | `false` |
| `allow-all-paths` | Allow access to any file path without approval. When `false` (default), the agent is scoped to the workspace instead of the entire filesystem | ❌ | `false` |
| `secret-env-vars` | Comma-separated env var names whose values are stripped from shell/MCP environments and redacted from output/logs (e.g., `"API_KEY,DB_PASSWORD"`) | ❌ | - |
| **MCP Configuration** | | | |
| `enable-all-github-mcp-tools` | Enable all GitHub MCP tools | ❌ | `false` |
| `add-github-mcp-tools` | Comma-separated list of specific GitHub MCP tools to enable | ❌ | - |
| `add-github-mcp-toolsets` | Comma-separated list of GitHub MCP toolsets to enable | ❌ | - |
| `disable-mcp-servers` | Comma-separated list of MCP servers to disable | ❌ | - |
| `disable-builtin-mcps` | Disable all built-in MCP servers (currently only `github-mcp-server`) | ❌ | `false` |
| `additional-mcp-config` | Comma-separated extra MCP server configs (JSON string or `@file` path) that augment the merged MCP config | ❌ | - |
| **Files & Directories** | | | |
| `additional-directories` | Comma-separated list of additional directories to trust | ❌ | - |
| `disallow-temp-dir` | Prevent automatic access to the system temp directory | ❌ | `false` |
| `plugin-dir` | Comma-separated local plugin directories to load | ❌ | - |
| **Session Management** | | | |
| `resume-session` | Resume from a previous session ID (use `"latest"` for most recent) | ❌ | - |
| `session-id` | Resume an existing session/task by ID, or set the UUID for a new session | ❌ | - |
| `name` | Set a human-friendly name for the session | ❌ | - |
| `enable-memory` | Enable cross-session memory in prompt mode | ❌ | `false` |
| `share` | Share session to a markdown file after completion | ❌ | - |
| `share-gist` | Share session to a secret GitHub gist | ❌ | `false` |
| **Output & Logging** | | | |
| `output-format` | Output format (`json` for JSONL output) | ❌ | - |
| `log-level` | Log level: `none`, `error`, `warning`, `info`, `debug`, `all` | ❌ | `all` |
| `upload-artifact` | Upload Copilot logs as workflow artifacts | ❌ | `true` |
| `fail-on-error` | Fail the step if Copilot CLI exits with non-zero code | ❌ | `false` |
| `copilot-version` | Version spec of the Copilot CLI to install. Examples: `1.0.80`, `1.x`, `>=1.0.80`, `latest`, `prerelease`. | ❌ | `1.0.80` |
| `options` | Additional CLI flags (e.g., `"--no-custom-instructions"`) | ❌ | `--screen-reader --no-color --stream off` |

### Pinning the Copilot CLI version

The action installs a pinned, known-good CLI by default so a CLI release can't
change your workflow's behavior overnight. `copilot-version` accepts any npm
version spec, so you choose your own tradeoff between stability and freshness:

```yaml
copilot-version: '1.0.80'      # exact pin (default)
copilot-version: '1.x'         # newest 1.x
copilot-version: '>=1.0.80'    # floor, no ceiling
copilot-version: 'latest'      # newest stable
copilot-version: 'prerelease'  # bleeding edge
```

> [!NOTE]
> `copilot-config` is written to `~/.copilot/settings.json`. CLI versions older
> than `1.0.35` read user settings from `config.json` instead — the action
> detects this and writes to the correct file, warning you when it does.

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
| [Actions Report](.github/workflows/copilot-actions-report.yml) | Analyzes the last 100 workflow runs and opens a workflow optimization report issue |
| [CI Fix](.github/workflows/copilot-ci-fix.yml) | Automatically analyzes failed workflow runs and creates a pull request with fixes |
| [Comment Trigger](.github/workflows/copilot-comment.yml) | Responds to issue comments starting with `/copilot` and executes the requested task |
| [Dependabot Analysis](.github/workflows/copilot-dependabot-update.yml) | Reviews Dependabot PRs with detailed dependency analysis, breaking changes, and migration guidance |
| [PR Review](.github/workflows/copilot-pr-review.yml) | Performs comprehensive autonomous code reviews on pull requests with severity-based feedback |
| [Research](.github/workflows/copilot-research.yml) | Conducts deep research on GitHub issues using Firecrawl to gather and synthesize information |
| [Security Triage](.github/workflows/copilot-security-triage.yml) | Triages all security alerts (Dependabot, Secret Scanning, Code Scanning) into a single comprehensive report |
| [Issue Triage](.github/workflows/copilot-labeler.yml) | Automatically labels issues based on their title and content using existing repository labels |
| [Usage Report](.github/workflows/copilot-usage-report.yml) | Generates comprehensive Copilot usage reports and analytics |

</details>

## Troubleshooting

> [!NOTE]
> Most issues stem from permissions configuration.

### Common Issues

1. **"Copilot token required" / "Authentication failed" / Permission Denied**
   - Ensure your workflow has `copilot-requests: write` permission
   - If you declare **any** `permissions:` block, you must list `copilot-requests: write` explicitly. Declaring a block drops every scope you do not name, and Copilot auth fails without it.
   - Your org must enable the **"Allow use of Copilot CLI billed to the organization"** policy
   - If using a legacy PAT, ensure it has the "Copilot Requests" permission

2. **Job is green but Copilot did nothing**
   - `fail-on-error` defaults to `false`, so a failed Copilot run still passes the step
   - Check the job log for a `Copilot CLI exited with code ...` warning, which usually means missing `copilot-requests: write`
   - Set `fail-on-error: true` to turn these into hard failures

3. **Copilot starts but permission denied on repo operations**
   - Add appropriate permissions (e.g., `contents: write`, `pull-requests: write`)
   - Check Settings > Actions > General > Workflow permissions

4. **Tool Access Denied**
   - Check your `allowed-tools` and `denied-tools` configuration
   - If `allow-all-tools: false`, you must explicitly allow needed tools

5. **MCP Server Connection Issues**
   - Verify MCP server URLs are accessible from GitHub-hosted runners
   - Check authentication headers and tokens
   - Ensure `type` is set correctly (`local`, `http`, or `sse`)

6. **Session Resume Not Working**
   - Session data is stored in logs; ensure `upload-artifact: true`
   - Use `resume-session: latest` to continue the most recent session

7. **Large Output Truncation**
   - Set `log-level: error` or `log-level: warning` to reduce verbosity
   - Break complex prompts into smaller, focused tasks

## Related Resources

- [GitHub Copilot CLI Documentation](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/)
- [MCP Server Configuration Docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/extend-coding-agent-with-mcp#writing-a-json-configuration-for-mcp-servers)
- [Search for workflow examples](https://github.com/search?q=%22copilot+-p%22+path%3A.github%2Fworkflows%2F*.yml&ref=opensearch&type=code)

