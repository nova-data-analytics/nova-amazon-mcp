# Microsoft Copilot

Nova connects through **Copilot Studio** as an MCP tool, then reaches Teams and Microsoft 365 Copilot through your agent. Availability depends on your organisation's configuration.

1. In Copilot Studio, open your agent, then **Tools** > **Add a tool** > **New tool** > **Model Context Protocol**
2. Fill in:

   | Field | Value |
   |---|---|
   | Server name | `Nova` |
   | Server URL | `https://mcp.novadata.io/api/mcp` |
   | Authentication | OAuth 2.0, Dynamic discovery |

3. Create a new connection, sign in to Nova, then **Add to agent**
4. Publish the agent to Teams and Microsoft 365 Copilot

```
What did Amazon order from us this week versus last?
```

Step by step: [novadata.io/amazon-ai-agents/connect-copilot](https://novadata.io/amazon-ai-agents/connect-copilot)
