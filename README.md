# GitHub Copilot CLI Action 🤖

A GitHub Action wrapper for the [GitHub Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli) that enables AI-powered automation in your workflow files.

## Installation

### Token Setup

> [!WARNING]
> The default `GITHUB_TOKEN` does **NOT** have Copilot permissions!

You need a **Personal Access Token (PAT)** with Copilot access.

**🚀 Quick Setup:** [Create Copilot CLI Token (Pre-configured)](https://github.com/settings/personal-access-tokens/new?name=Copilot+CLI+Token&description=GitHub+Copilot+CLI+automation+for+workflows&expires_in=90&user_copilot_requests=read)

At minimum, you need: **`Copilot Requests = Read-only`**

> [!TIP]
> Save your token as a repository secret named `COPILOT_TOKEN`

#### Organization Repositories

> [!IMPORTANT]
> **Fine-grained PAT Limitation:** Organization-owned tokens cannot have the "Copilot Requests" permission — this is a GitHub platform limitation.
>
> **Workaround for Organization Repos:**
> 1. Use a **user-owned PAT** with "Copilot Requests" permission for `copilot-token`
> 2. Use the default `GITHUB_TOKEN` or an org token for `repo-token` (for repository operations)
>
> ```yaml
> - uses: austenstone/copilot-cli@v2
>   with:
>     copilot-token: ${{ secrets.COPILOT_TOKEN }}  # User PAT with Copilot access
>     repo-token: ${{ secrets.GITHUB_TOKEN }}      # Default token for repo operations
>     prompt: "Your prompt here"
> ```
>
> This two-token pattern allows Copilot access while maintaining proper repository permissions.

### Basic Setup

Add the following workflow to your `.github/workflows` folder:

```yaml
name: 'Copilot Automation'
on: [pull_request]

jobs:
  copilot:
    permissions:
      pull-requests: write
    runs-on: ubuntu-latest
    steps:
      - name: 'Checkout Repository'
        uses: actions/checkout@v5

      - name: 'Run Copilot CLI'
        uses: austenstone/copilot-cli@v2
        with:
          copilot-token: ${{ secrets.COPILOT_TOKEN }}
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
| `copilot-token` | PAT with "Copilot Requests" permission. **The default `github.token` does NOT work** — you must provide a PAT. | ✅ | - |
| `prompt` | Natural language prompt to send to GitHub Copilot | ✅ | - |
| `repo-token` | Token for standard GitHub repo operations (push, PRs). Falls back to `copilot-token` if not set. Can use default `GITHUB_TOKEN` here. | ❌ | `github.token` |
| `mcp-config` | MCP server configuration in JSON format | ❌ | - |
| `copilot-config` | GitHub Copilot CLI configuration (JSON) | ❌ | See below |
| `allow-all-tools` | Allow all tools without approval | ❌ | `true` |
| `allowed-tools` | Comma-separated list of tools to allow (e.g., `"shell(rm),shell(git push)"`) | ❌ | - |
| `denied-tools` | Comma-separated list of tools to deny (e.g., `"shell(rm),shell(git push)"`) | ❌ | - |
| `copilot-version` | Version of `@github/copilot` to install (e.g., `"latest"`, `"0.0.329"`) | ❌ | `latest` |
| `model` | AI model to use (e.g., `"claude-sonnet-4.5"`, `"gpt-5"`) | ❌ | - |
| `agent` | Specify a custom agent to use | ❌ | - |
| `additional-directories` | Comma-separated list of additional directories to trust (e.g., `"/tmp,/var/log"`) | ❌ | - |
| `disable-mcp-servers` | Comma-separated list of MCP servers to disable (e.g., `"github-mcp-server,custom-server"`) | ❌ | - |
| `enable-all-github-mcp-tools` | Enable all GitHub MCP tools | ❌ | `false` |
| `resume-session` | Resume from a previous session ID (use `"latest"` for most recent) | ❌ | - |
| `log-level` | Log level: `"none"`, `"error"`, `"warning"`, `"info"`, `"debug"`, `"all"`, `"default"` | ❌ | `all` |
| `upload-artifact` | Upload Copilot logs as workflow artifacts | ❌ | `true` |

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
> Most issues stem from token configuration.

### Common Issues

1. **"Copilot token required" / Permission Denied**
   - The default `GITHUB_TOKEN` does NOT have Copilot access
   - You must use a PAT with the "Copilot Requests" permission
   - Make sure your token is saved as a secret and referenced correctly

2. **Organization Repository Access Issues**
   - **Problem:** Fine-grained PATs cannot have both "Copilot Requests" permission AND organization repository access
   - **Root Cause:** This is a GitHub platform limitation:
     - Personal account tokens: ✅ Copilot Requests permission, ❌ Limited org repo access
     - Organization tokens: ❌ No Copilot Requests permission, ✅ Full org repo access
   - **Solution:** Use the two-token pattern:
     ```yaml
     - uses: austenstone/copilot-cli@v2
       with:
         copilot-token: ${{ secrets.COPILOT_TOKEN }}  # User PAT with Copilot Requests
         repo-token: ${{ secrets.GITHUB_TOKEN }}      # Default token for repo operations
     ```
   - The `copilot-token` authenticates with Copilot API
   - The `repo-token` handles repository operations (commits, PRs, etc.)
   - Ensure your user PAT has appropriate repository access for the repositories you want Copilot to analyze

3. **Copilot starts but permission denied**
   - The repo-token default to `GITHUB_TOKEN`.
   - Add `permissions: write-all` to your workflow file.
   - Check Settings > Actions > General > Workflow permissions.
   - Verify the token is correctly configured in your workflow.

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

