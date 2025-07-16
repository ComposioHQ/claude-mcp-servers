---
name: Open-WebSearch MCP Server
digest: A Model Context Protocol (MCP) server based on multi-engine search results, supporting free web search without API keys.
author: Aas-ee
repository: https://github.com/Aas-ee/open-webSearch
capabilities:
  prompts: false
  resources: false
  tools: true
tags:
  - web-search
  - search
  - web
icon: https://avatars.githubusercontent.com/u/81606643?s=48&v=4
createTime: 2025-06-10
---

A Model Context Protocol (MCP) server based on multi-engine search results, supporting free web search without API keys.

## Features

- Web search using multi-engine results
  - bing
  - baidu
  - ~~linux.do~~ temporarily unsupported
  - csdn
  - duckduckgo
  - exa
  - brave
- HTTP proxy configuration support for accessing restricted resources
- No API keys or authentication required
- Returns structured results with titles, URLs, and descriptions
- Configurable number of results per search
- Customizable default search engine
- Support for fetching individual article content
  - csdn

## TODO

- Support for ~~Bing~~ (already supported), ~~DuckDuckGo~~ (already supported), ~~Exa~~ (already supported), ~~Brave~~ (already supported), Google and other search engines
- Support for more blogs, forums, and social platforms
- Optimize article content extraction, add support for more sites

## Installation Guide

### Local Installation

1. Clone or download this repository
2. Install dependencies:

```bash
npm install
```

3. Build the server:

```bash
npm run build
```

4. Add the server to your MCP configuration:

**Cherry Studio:**

```json
{
  "mcpServers": {
    "web-search": {
      "name": "Web Search MCP",
      "type": "streamableHttp",
      "description": "Multi-engine web search with article fetching",
      "isActive": true,
      "baseUrl": "http://localhost:3000/mcp"
    }
  }
}
```

**VSCode (Claude Dev Extension):**

```json
{
  "mcpServers": {
    "web-search": {
      "transport": {
        "type": "streamableHttp",
        "url": "http://localhost:3000/mcp"
      }
    },
    "web-search-sse": {
      "transport": {
        "type": "sse",
        "url": "http://localhost:3000/sse"
      }
    }
  }
}
```

**Claude Desktop:**

```json
{
  "mcpServers": {
    "web-search": {
      "transport": {
        "type": "streamableHttp",
        "url": "http://localhost:3000/mcp"
      }
    },
    "web-search-sse": {
      "transport": {
        "type": "sse",
        "url": "http://localhost:3000/sse"
      }
    }
  }
}
```

### Docker Deployment

Quick deployment using Docker Compose:

```bash
docker-compose up -d
```

Or use Docker directly:

```bash
docker run -d --name web-search -p 3000:3000 -e ENABLE_CORS=true -e CORS_ORIGIN=* ghcr.io/aas-ee/open-web-search:latest
```

Environment variable configuration:

```bash
# Enable CORS (default: false)
ENABLE_CORS=true

# CORS origin configuration (default: *)
CORS_ORIGIN=*

# Default search engine (options: bing, duckduckgo, exa, brave, default: bing)
DEFAULT_SEARCH_ENGINE=duckduckgo

# Enable HTTP proxy (default: false)
USE_PROXY=true

# Proxy server URL (default: http://127.0.0.1:10809)
PROXY_URL=http://your-proxy-server:port
```

Then configure in your MCP client:

```json
{
  "mcpServers": {
    "web-search": {
      "name": "Web Search MCP",
      "type": "streamableHttp",
      "description": "Multi-engine web search with article fetching",
      "isActive": true,
      "baseUrl": "http://localhost:3000/mcp"
    },
    "web-search-sse": {
      "transport": {
        "name": "Web Search MCP",
        "type": "sse",
        "description": "Multi-engine web search with article fetching",
        "isActive": true,
        "url": "http://localhost:3000/sse"
      }
    }
  }
}
```

## Usage Guide

The server provides three tools: `search`, `fetchLinuxDoArticle`, and `fetchCsdnArticle`.

### search Tool Usage

```typescript
{
  "query": string,        // Search query
  "limit": number,        // Optional: Number of results to return (default: 10)
  "engines": string[]     // Optional: Engines to use (bing,baidu,linuxdo,csdn,duckduckgo,exa,brave) default bing
}
```

Usage example:

```typescript
use_mcp_tool({
  server_name: "web-search",
  tool_name: "search",
  arguments: {
    query: "search content",
    limit: 3, // Optional parameter
    engines: ["bing", "csdn", "duckduckgo", "exa", "brave"], // Optional parameter, supports multi-engine combined search
  },
});
```

Response example:

```json
[
  {
    "title": "Example Search Result",
    "url": "https://example.com",
    "description": "Description text of the search result...",
    "source": "Source",
    "engine": "Engine used"
  }
]
```

### fetchCsdnArticle Tool Usage

Used to fetch complete content of CSDN blog articles.

```typescript
{
  "url": string    // URL from CSDN search results using the search tool
}
```

Usage example:

```typescript
use_mcp_tool({
  server_name: "web-search",
  tool_name: "fetchCsdnArticle",
  arguments: {
    url: "https://blog.csdn.net/xxx/article/details/xxx",
  },
});
```

Response example:

```json
[
  {
    "content": "Example search result"
  }
]
```

### fetchLinuxDoArticle Tool Usage

Used to fetch complete content of Linux.do forum articles.

```typescript
{
  "url": string    // URL from linuxdo search results using the search tool
}
```

Usage example:

```typescript
use_mcp_tool({
  server_name: "web-search",
  tool_name: "fetchLinuxDoArticle",
  arguments: {
    url: "https://xxxx.json",
  },
});
```

Response example:

```json
[
  {
    "content": "Example search result"
  }
]
```

## Usage Limitations

Since this tool works by scraping multi-engine search results, please note the following important limitations:

1. **Rate Limiting**:

   - Too many searches in a short time may cause the used engines to temporarily block requests
   - Recommendations:
     - Maintain reasonable search frequency
     - Use the limit parameter judiciously
     - Add delays between searches when necessary

2. **Result Accuracy**:

   - Depends on the HTML structure of corresponding engines, may fail when engines update
   - Some results may lack metadata like descriptions
   - Complex search operators may not work as expected

3. **Legal Terms**:

   - This tool is for personal use only
   - Please comply with the terms of service of corresponding engines
   - Implement appropriate rate limiting based on your actual use case

4. **Search Engine Configuration**:

   - Default search engine can be set via the `DEFAULT_SEARCH_ENGINE` environment variable
   - Supported engines: bing, duckduckgo, exa, brave
   - The default engine is used when searching specific websites

5. **Proxy Configuration**:
   - HTTP proxy can be configured when certain search engines are unavailable in specific regions
   - Enable proxy with environment variable `USE_PROXY=true`
   - Configure proxy server address with `PROXY_URL`
