# Claude (web and desktop)

Nova MCP is a remote server, so it is added as a **custom connector**, not as a local command. The Free plan allows one custom connector; paid plans allow more. On Team and Enterprise, an Owner adds it once in Organization settings.

1. Open **Customize** then **Connectors**
2. Click **Add custom connector** and fill in:

   | Field | Value |
   |---|---|
   | Name | `Nova` |
   | Remote MCP server URL | `https://mcp.novadata.io/api/mcp` |

3. Choose **Sign in now**, click **Add**, then sign in to Nova and allow access
4. Turn **Nova** on in a chat

Ask a first question to confirm it works:

```
Give me last week's P&L by marketplace and name the three biggest swings.
```

Step by step with screenshots: [novadata.io/amazon-ai-agents/connect-claude](https://novadata.io/amazon-ai-agents/connect-claude)
