# Nebula MCP server (read-only)

A public, read-only Model Context Protocol (MCP) server with facts about
Nebula Personalization Tech Solutions Pvt. Ltd., a digital marketing agency in
Mumbai, India (https://www.nebulatech.in/).

This repository holds documentation and registry metadata only. It contains no
server source code.

- Endpoint: `https://www.nebulatech.in/_functions/mcp`
- Transport: Streamable HTTP (stateless JSON-RPC 2.0 over POST)
- Authentication: none (public data only)
- Server name: `nebula`, version 1.4.1, protocol version 2025-06-18
- MCP Registry name: `in.nebulatech/nebula`

## What it covers

Data comes from the Nebula Knowledge Graph, the same source as the public
knowledge base on nebulatech.in. Content is English only.

- About: organisation facts, founders, services, industries served and the
  RASA research framework
- Answers: published Answers pages; reading one Answer returns its full body
  as Markdown (`fullAnswer`)
- Case studies: published case studies (as reported on each case study page)
- Blogs: a list of blog posts, plus full article Markdown via
  `nebula_blog_get` / `nebula_blog_search` for active published English posts

The server cannot change anything. All tools are marked
`readOnlyHint: true`, `destructiveHint: false`, `idempotentHint: true` and
`openWorldHint: false`.

## Tools

| Tool | Input | Returns |
|---|---|---|
| `nebula_overview` | none | Organisation facts, founders, services, industries, frameworks, counts |
| `nebula_search` | `query` (string, required), `limit` (integer 1-20), `offset` (integer >= 0) | Ranked results with path/score/record, plus total, offset, limit, nextOffset |
| `nebula_list` | `section` (optional), `limit` (integer 1-100), `offset` (integer >= 0) | Items with title/url/section/date, plus total, offset, limit, nextOffset |
| `nebula_section` | `path` (string, required), `offset` (integer >= 0), `limit` (integer 1-50, default 20) | One section or record by dotted path; Answers include `fullAnswer` Markdown |
| `nebula_blog_get` | `slug` or `url` or `index`, optional `contentOffset` | Full Markdown body of one active English blog post, with canonicalUrl, publisher, datePublished, dateModified, language; paginated via contentOffset |
| `nebula_blog_search` | `query` (string, required), `limit` (integer 1-20) | Ranked full-text search across English blog posts with matched snippets |

Sections for `nebula_list` and `nebula_section`: organization, people,
services, industries, frameworks, research, externalAssets, pages, answers,
caseStudies, blogs. Blog list entries stay compact (title, url, excerpt,
date); use `nebula_blog_get` for the full article. Examples for
`nebula_section`: `services`, `answers.0`, `caseStudies.0`,
`organization.address`.

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

## Changelog

### 1.4.1

- `nebula_blog_get` resolves old or redirected blog slugs to the current post
  (returns `requestedSlug` and `redirectedFrom`); retired slugs return
  `Blog post retired: {slug}`
- `nebula_blog_search` output always stays valid JSON with `truncated` /
  `nextOffset`
- Still six read-only tools

### 1.4.0

- Added `nebula_blog_get`: full Markdown text of any active published English
  blog post by slug or URL (canonical URL, publisher, datePublished,
  dateModified, language; paginated via `contentOffset`)
- Added `nebula_blog_search`: ranked full-text search across English blog
  posts with matched snippets
- Six read-only tools total

### 1.3.0

- Answers return full body Markdown (`fullAnswer`; 120 Answers)
- Added `caseStudies` section (18 case studies)
- Not published to the MCP Registry (listing goes from 1.2.0 to 1.4.0)

### 1.2.0

- Documented four read-only tools including `nebula_list`

## Links

- Website: https://www.nebulatech.in/
- llms.txt: https://www.nebulatech.in/llms.txt

## License

Documentation in this repository is released under the MIT License. See
[LICENSE](LICENSE).
