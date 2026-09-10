# Claude Desktop and Claude web

Nova MCP is a remote server, so it is added as a **custom connector**, not as a local command.

Requires a paid Claude plan. If you do not see "Add custom connector", that is why.

1. Open Claude, go to **Settings** then **Connectors**
2. Click **Add**, then **Add custom connector**
3. Fill in:

   | Field | Value |
   |---|---|
   | Name | `Nova` |
   | Remote MCP server URL | `https://mcp.novadata.io/api/mcp` |

4. Click **Add**, then complete the OAuth sign in with your Nova account
5. Toggle **Nova** on in the connectors list

Ask a first question to confirm it works:

```
List my Amazon seller accounts and the metrics available.
```

If the connector fails to authorise, check that your Nova account has Seller Central connected and has completed at least one hourly sync.
