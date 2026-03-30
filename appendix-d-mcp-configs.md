# Appendix D — .mcp.json Templates by Project Level

The `.mcp.json` file is placed in the root of each project.
Configuration depends on the project level (see standard section 2).

> **MCP (Model Context Protocol)** — an open protocol for connecting AI agents to external tools and data sources. Specific MCP servers are chosen based on the tools adopted by the company. The examples below are reference configurations.

**MCP server selection principles:**
- Each server should save context window space, not pollute it
- Prefer MCP servers that return targeted data over raw arrays
- Do not connect servers that duplicate the model's built-in capabilities

---

## Mandatory MCP Server Categories

Regardless of specific products, each project must have MCP servers in the following categories:

| Category | Purpose | L1 | L2 | L3 | Implementation Examples |
|----------|---------|----|----|----|-----------------------|
| **Memory** | Persistent context between sessions | + | + | + | @modelcontextprotocol/server-memory |
| **Library documentation** | Targeted access to documentation without polluting context | + | + | -- | context7, devdocs |
| **Git** | Structured git operations (diff, log, branch) | + | + | + | mcp-server-git |
| **Task tracker** | Task management, issue lifecycle | + | + | + | Atlassian MCP, Linear MCP, self-hosted |
| **Knowledge base** | Syncing docs/ with corporate wiki | + | + | + | Atlassian MCP, Notion MCP, self-hosted |
| **Code navigation** | Semantic navigation (go to definition, find references) | + | + | + | serena, sourcegraph |

> For L3, all MCP servers must be deployed within the company perimeter. Cloud endpoints are prohibited.

### Optional Categories

| Category | Purpose | L1 | L2 | L3 | Implementation Examples |
|----------|---------|----|----|----|-----------------------|
| **HTTP client** | Loading external resources and APIs | + | + | -- | fetch |
| **Database** | Database access via a controlled interface | -- | + | + | server-postgres, server-mysql |

---

## Reference Configurations

### L1 — Public / Internal

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "git": {
      "command": "uvx",
      "args": ["mcp-server-git", "--repository", "."]
    },
    "task-tracker": {
      "type": "url",
      "url": "${TASK_TRACKER_MCP_URL}"
    },
    "knowledge-base": {
      "type": "url",
      "url": "${KNOWLEDGE_BASE_MCP_URL}"
    },
    "code-navigation": {
      "command": "uvx",
      "args": ["serena"]
    }
  }
}
```

> For the Atlassian stack: a single MCP endpoint `https://mcp.atlassian.com/v1/mcp` covers both the task tracker (Jira) and knowledge base (Confluence). Authentication — OAuth 2.1 or API token.

---

### L2 — Confidential

Differences from L1: added MCP for database access via a controlled interface.

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "git": {
      "command": "uvx",
      "args": ["mcp-server-git", "--repository", "."]
    },
    "task-tracker": {
      "type": "url",
      "url": "${TASK_TRACKER_MCP_URL}"
    },
    "knowledge-base": {
      "type": "url",
      "url": "${KNOWLEDGE_BASE_MCP_URL}"
    },
    "code-navigation": {
      "command": "uvx",
      "args": ["serena"]
    },
    "database": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "${POSTGRES_CONNECTION_STRING}"
      }
    }
  }
}
```

---

### L3 — Restricted

Differences from L2:
- **Library documentation** is disabled — external network requests are prohibited
- All MCP servers are **self-hosted** within the company perimeter
- Task tracker and knowledge base connect via internal endpoints

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "git": {
      "command": "uvx",
      "args": ["mcp-server-git", "--repository", "."]
    },
    "task-tracker": {
      "command": "npx",
      "args": ["-y", "${TASK_TRACKER_MCP_PACKAGE}"],
      "env": {
        "TRACKER_BASE_URL": "${TRACKER_INTERNAL_URL}",
        "TRACKER_API_TOKEN": "${TRACKER_API_TOKEN}"
      }
    },
    "knowledge-base": {
      "command": "npx",
      "args": ["-y", "${KNOWLEDGE_BASE_MCP_PACKAGE}"],
      "env": {
        "KB_BASE_URL": "${KB_INTERNAL_URL}",
        "KB_TOKEN": "${KB_TOKEN}"
      }
    },
    "code-navigation": {
      "command": "uvx",
      "args": ["serena"]
    },
    "database": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "${POSTGRES_CONNECTION_STRING}"
      }
    }
  }
}
```

> **L3:** Specific MCP packages for the self-hosted tracker and knowledge base are determined by the security department. The packages in the example above are placeholders.

> All environment variables (`${...}`) are set via the corporate secrets store. No values directly in `.mcp.json`.

---

## Not Recommended MCP Servers

| MCP Server | Reason |
|------------|--------|
| sequential-thinking | Duplicates native extended thinking in modern LLMs. Each "thought" is a tool call, consuming tokens and polluting context. The model's built-in reasoning is more efficient. |
| time | Modern LLMs receive the current date/time from the system prompt. The MCP server provides no additional value. |
