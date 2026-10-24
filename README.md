# Zeabur MCP Bridge

Hosted HTTP bridge for the Zeabur MCP server. The official @zeabur/mcp-server runs as a local stdio process (npx @zeabur/mcp-server with a ZEABUR_TOKEN), which cannot be registered directly in Composio because Composio custom MCP entries require a remote HTTP URL. This repository wraps the official server with an MCP stdio-to-HTTP proxy (mcp-proxy) so it can be deployed as a service and registered remotely.

## Architecture

- @zeabur/mcp-server: the official Zeabur MCP server (projects, deployments, environment variables, logs).
- mcp-proxy: bridges the server's stdio transport to HTTP (Streamable HTTP / SSE).
- Deployed on Zeabur as a Node service (zbpack.json, type node, port 8080).

## Deploy on Zeabur

1. Open the Zeabur dashboard and create a project.
2. Add a service from this GitHub repository (hillstreet-ph/zeabur-mcp-bridge).
3. Set the environment variables:
   - ZEABUR_TOKEN: your Zeabur API token (Zeabur dashboard, Settings, API tokens).
   - PORT: 8080 (default).
4. Zeabur builds with zbpack.json (type node) automatically. Bind a domain or use the service's default *.zeabur.app URL.
5. The MCP endpoint is the service URL, for example https://zeabur-mcp-bridge.zeabur.app

## Security

The endpoint exposes full control of your Zeabur account. Protect it before registering it publicly:
- Option A: keep the service private within a Zeabur private network and expose it through an authenticated gateway (for example, an open-tgate.site route with auth_required and an API key).
- Option B: bind a custom domain and restrict access at the edge (Cloudflare Access) before sharing the URL.

## Register in Composio

1. Open https://dashboard.composio.dev/~/org/connect/apps?add-custom-mcp=true
2. Display name: Zeabur MCP Bridge; MCP server URL: your deployed service URL from above.
3. Authentication: none if the endpoint is network-protected, or Bearer token if routed through an authenticated gateway.
4. Remove the old broken connection (custom_zeabur_mcp_bridge_oord-anacid), which returns HTTP 500, after the new registration syncs successfully.

## Sync to open-connect.site connectors

After the toolkit is active in Composio, the Zeabur tools (29 documented in the Composio skill "Zeabur MCP Bridge Toolkit") become available to the open-connect.site hub, which manages connectors for all apps: open-system.space, open-box.space, open-teleset.site, open-tgate.site, and open-connect.site.

## Local alternative (Claude Desktop / Cursor)

```json
{
  "mcpServers": {
    "zeabur": {
      "command": "npx",
      "args": ["@zeabur/mcp-server"],
      "env": { "ZEABUR_TOKEN": "<YOUR_TOKEN>" }
    }
  }
}
```
