---
{"dg-publish":true,"permalink":"/vault/claude-code/multiple-linear-workspaces/"}
---

Hi there
Add this to `~/.claude.json`

```
  "mcpServers": {
    "strive-linear": {
      "command": "npx",
      "args": [
        "mcp-server-linear"
      ],
      "env": {
        "LINEAR_ACCESS_TOKEN": "api-key-here",
        "TOOL_PREFIX": "strive"
      }
    },
    "openchat-linear": {
      "command": "npx",
      "args": [
        "mcp-server-linear"
      ],
      "env": {
        "LINEAR_ACCESS_TOKEN": "api-key-here",
        "TOOL_PREFIX": "openchat"
      }
    },
    "river-linear": {
      "command": "npx",
      "args": [
        "mcp-server-linear"
      ],
      "env": {
        "LINEAR_ACCESS_TOKEN": "api-key-here",
        "TOOL_PREFIX": "river"
      }
    }
  }
  ```