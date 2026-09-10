# Claude Code

Add Nova MCP as a remote HTTP server:

```bash
claude mcp add --transport http nova https://mcp.novadata.io/api/mcp
```

Then authenticate:

```bash
claude
/mcp
```

Select **nova** and complete the OAuth flow in the browser.

Verify:

```
> Which SKUs lost the most net profit versus last month?
```

To scope the server to a single project instead of your user config, add `--scope project`, which writes to `.mcp.json` in the working directory.
