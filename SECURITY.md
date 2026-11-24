# Security

Nova MCP is a hosted service operated by Nova Data Analytics. This repository holds client configurations, documentation, prompts and skills. It does not contain the server code.

## Reporting a vulnerability

Email **contact@novadata.io** with "Security" in the subject line. Please include:

- what you found and where (URL, tool name, client used)
- the steps to reproduce it
- the impact you expect

Please do not open a public GitHub issue for a security problem. We reply to every report and keep you informed until it is resolved.

## Scope

- The MCP endpoint `https://mcp.novadata.io/api/mcp` and its OAuth sign-in
- The Nova app at `https://app.novadata.io`
- The files in this repository

## How access works

- Clients connect with OAuth 2.0 (PKCE, dynamic client registration) using a Nova account. Access is scoped to the user's Nova workspace and can be revoked in Nova at any time.
- Tools read data by default. The only tools that change data on Amazon are Seller Central listing content edits (never price or quantity): they work for the workspace owner only, and every change is previewed with Amazon's validation before it is confirmed.
- Other write tools only change settings stored in Nova (costs, VAT, stock parameters, supplier orders, TACOS targets, tags, events).
