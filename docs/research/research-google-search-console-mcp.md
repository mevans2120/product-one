# Google Search Console MCP Servers

Research into available MCP (Model Context Protocol) servers for Google Search Console.

## Summary

There is no official MCP server from Google. Several community-built options exist, all read-only and compatible with any MCP client over stdio.

## Options

### 1. mcp-server-gsc (by ahonn)

- **Repo:** https://github.com/ahonn/mcp-server-gsc
- **Language:** TypeScript/Node.js
- **Install:** `npx -y mcp-server-gsc`
- **Auth:** Google service account
- **Features:**
  - Up to 25,000 rows of search analytics data
  - Advanced filtering with regex patterns and multiple filter operators
  - Quick wins detection (auto-identifies optimization opportunities)
  - Dimensions: query, page, country, device, search appearance
  - One-click setup for Claude Desktop, Cursor, and Raycast

### 2. mcp-gsc (by AminForou)

- **Repo:** https://github.com/AminForou/mcp-gsc
- **Language:** Python
- **Auth:** OAuth or service account
- **Notes:** Designed specifically for SEO workflows. Requires Node.js for MCP inspector.

### 3. searchconsole-mcp (by chrishart0)

- **Repo:** https://github.com/chrishart0/searchconsole-mcp
- **Language:** Python (uv)
- **Install:** `uv run --directory /path/to/searchconsole-mcp searchconsole-mcp`
- **Features:** Read-only access to keywords, clicks, impressions, CTR, rankings, sitemap status.

### 4. Coupler.io MCP

- **URL:** https://www.coupler.io/mcp/google-search-console
- **Type:** Managed service
- **Features:** Bridges GSC data to MCP clients. Also supports 70+ other data sources (Google Analytics, Google Ads, Facebook Ads, HubSpot, Salesforce, etc.).

## Notes

- All options are read-only and do not modify anything in GSC.
- Any MCP-compatible client can connect (not limited to Claude Desktop).
- mcp-server-gsc appears to be the most mature and feature-rich open-source option.
