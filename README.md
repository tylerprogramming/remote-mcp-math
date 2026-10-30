# Remote MCP Math Server on Cloudflare (Without Auth)

A remote MCP server with four math tools, running on Cloudflare Workers without authentication. It started from Cloudflare's [remote-mcp-authless](https://github.com/cloudflare/ai/tree/main/demos/remote-mcp-authless) template and adds the `sqrt` and `power` tools.

## Tools

Defined in the `init()` method of `src/index.ts`:

| Tool | Arguments | Returns |
|---|---|---|
| `add` | `a`, `b` | `a + b` |
| `calculate` | `operation` (`add`, `subtract`, `multiply`, `divide`), `a`, `b` | the result, or an error on divide by zero |
| `sqrt` | `number` | the square root, or an error for a negative number |
| `power` | `base`, `exponent` | `base ** exponent` |

The server answers on `/sse` (SSE transport) and `/mcp` (streamable HTTP).

## Get started

Run it locally:

```bash
npm install
npm run dev   # wrangler dev, serves http://localhost:8787
```

Deploy it to your Cloudflare account:

```bash
npm run deploy   # wrangler deploy
```

Or use the button, which deploys this repo:

[![Deploy to Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/tylerprogramming/remote-mcp-math)

The Worker name is `remote-mcp-math` (from `wrangler.jsonc`), so it deploys to a URL like `remote-mcp-math.<your-account>.workers.dev/sse`.

## Customizing your MCP Server

To add your own [tools](https://developers.cloudflare.com/agents/model-context-protocol/tools/) to the MCP server, define each tool inside the `init()` method of `src/index.ts` using `this.server.tool(...)`.

## Connect to Cloudflare AI Playground

You can connect to your MCP server from the Cloudflare AI Playground, which is a remote MCP client:

1. Go to https://playground.ai.cloudflare.com/
2. Enter your deployed MCP server URL (`remote-mcp-math.<your-account>.workers.dev/sse`)
3. You can now use your MCP tools directly from the playground!

## Connect Claude Desktop to your MCP server

You can also connect to your remote MCP server from local MCP clients, by using the [mcp-remote proxy](https://www.npmjs.com/package/mcp-remote).

To connect to your MCP server from Claude Desktop, follow [Anthropic's Quickstart](https://modelcontextprotocol.io/quickstart/user) and within Claude Desktop go to Settings > Developer > Edit Config.

Update with this configuration:

```json
{
  "mcpServers": {
    "math": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "http://localhost:8787/sse"  // or remote-mcp-math.your-account.workers.dev/sse
      ]
    }
  }
}
```

Restart Claude and you should see the `add`, `calculate`, `sqrt` and `power` tools become available.
