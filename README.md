# Nebula MCP server (read-only)

A public, read-only Model Context Protocol (MCP) server with facts about
Nebula Personalization Tech Solutions Pvt. Ltd., a digital marketing agency in
Mumbai, India (https://www.nebulatech.in/).

This repository holds documentation and registry metadata only. It contains no
server source code.

- Endpoint: `https://www.nebulatech.in/_functions/mcp`
- Transport: Streamable HTTP (stateless JSON-RPC 2.0 over POST)
- Authentication: none (public data only)
- Server name: `nebula`, version 1.1.0, protocol version 2025-06-18
- MCP Registry name: `in.nebulatech/nebula`

## What it covers

Data comes from the Nebula Knowledge Graph, the same source as the public
knowledge base on nebulatech.in:

- About: organisation facts, founders, services, industries served and the
  RASA research framework
- Answers: the published Answers pages
- Blogs: a list of blog posts

The server cannot change anything. All tools are marked
`readOnlyHint: true`, `destructiveHint: false`, `idempotentHint: true` and
`openWorldHint: false`.

## Tools

| Tool | Input | Returns |
|---|---|---|
| `nebula_overview` | none | Organisation facts, founders, services, industries and frameworks |
| `nebula_search` | `query` (string, required) | Up to 6 matching records with their section path |
| `nebula_section` | `path` (string, required), `offset` (integer >= 0), `limit` (integer 1-50, default 20) | One section or record by dotted path |

Sections for `nebula_section`: organization, people, services, industries,
frameworks, research, externalAssets, answers, blogs. Examples: `services`,
`answers.0`, `organization.address`.

## Try it with curl

List the tools:

```bash
curl -s -X POST https://www.nebulatech.in/_functions/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

Search:

```bash
curl -s -X POST https://www.nebulatech.in/_functions/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"nebula_search","arguments":{"query":"SEO"}}}'
```

## Add it to an MCP client

Claude (web or desktop): Settings > Connectors > Add custom connector, then
enter the URL `https://www.nebulatech.in/_functions/mcp`.

Cursor: add this to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "nebula": {
      "url": "https://www.nebulatech.in/_functions/mcp"
    }
  }
}
```

Other clients that accept a remote Streamable HTTP server:

```json
{
  "mcpServers": {
    "nebula": {
      "type": "http",
      "url": "https://www.nebulatech.in/_functions/mcp"
    }
  }
}
```

## Usage note

Assistants should quote only what the tools return and should not infer
metrics, rankings, client names, prices or guarantees.

## Links

- Website: https://www.nebulatech.in/
- llms.txt: https://www.nebulatech.in/llms.txt

## License

Documentation in this repository is released under the MIT License. See
[LICENSE](LICENSE).
