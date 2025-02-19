# FireCrawl MCP Server

A Model Context Protocol (MCP) server implementation that integrates with FireCrawl for advanced web scraping capabilities.

## Features

- Web scraping with JavaScript rendering
- Efficient batch processing with built-in rate limiting
- URL discovery and crawling
- Web search with content extraction
- Automatic retries with exponential backoff
- Comprehensive logging system
- Support for cloud and self-hosted FireCrawl instances
- Mobile/Desktop viewport support
- Smart content filtering with tag inclusion/exclusion

## Installation

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/pashpashpash/mcp-server-firecrawl.git
   cd mcp-server-firecrawl
   ```

2. **Install Dependencies**:
   ```bash
   npm install
   ```

3. **Build the Project**:
   ```bash
   npm run build
   ```

## Configuration

### Environment Variables

#### Required for Cloud API

- `FIRE_CRAWL_API_KEY`: Your FireCrawl API key
  - Required when using cloud API (default)
  - Optional when using self-hosted instance with `FIRE_CRAWL_API_URL`
- `FIRE_CRAWL_API_URL` (Optional): Custom API endpoint for self-hosted instances
  - Example: `https://firecrawl.your-domain.com`
  - If not provided, the cloud API will be used (requires API key)

#### Optional Configuration

##### Retry Configuration

- `FIRE_CRAWL_RETRY_MAX_ATTEMPTS`: Maximum number of retry attempts (default: 3)
- `FIRE_CRAWL_RETRY_INITIAL_DELAY`: Initial delay in milliseconds before first retry (default: 1000)
- `FIRE_CRAWL_RETRY_MAX_DELAY`: Maximum delay in milliseconds between retries (default: 10000)
- `FIRE_CRAWL_RETRY_BACKOFF_FACTOR`: Exponential backoff multiplier (default: 2)

##### Credit Usage Monitoring

- `FIRE_CRAWL_CREDIT_WARNING_THRESHOLD`: Credit usage warning threshold (default: 1000)
- `FIRE_CRAWL_CREDIT_CRITICAL_THRESHOLD`: Credit usage critical threshold (default: 100)

### Usage with Claude Desktop

Add this to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "mcp-server-firecrawl": {
      "command": "node",
      "args": ["path/to/build/index.js"],
      "env": {
        "FIRE_CRAWL_API_KEY": "YOUR_API_KEY_HERE",
        "FIRE_CRAWL_RETRY_MAX_ATTEMPTS": "5",
        "FIRE_CRAWL_RETRY_INITIAL_DELAY": "2000",
        "FIRE_CRAWL_RETRY_MAX_DELAY": "30000",
        "FIRE_CRAWL_RETRY_BACKOFF_FACTOR": "3",
        "FIRE_CRAWL_CREDIT_WARNING_THRESHOLD": "2000",
        "FIRE_CRAWL_CREDIT_CRITICAL_THRESHOLD": "500"
      }
    }
  }
}
```
Note: Replace "path/to/build/index.js" with the **actual path** to your built index.js file. KEEP IN MIND, by default it will be in `./dist/src/index.js` according to `tsconfig.json`:

```
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "./dist",
    "rootDir": ".",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*", "jest.config.js", "jest.setup.ts"],
  "exclude": ["node_modules", "dist", "tests"]
}
```

### System Configuration

The server includes several configurable parameters that can be set via environment variables. Here are the default values if not configured:

```typescript
const CONFIG = {
  retry: {
    maxAttempts: 3,
    initialDelay: 1000,
    maxDelay: 10000,
    backoffFactor: 2,
  },
  credit: {
    warningThreshold: 1000,
    criticalThreshold: 100,
  },
};
```

## Available Tools

### 1. Scrape Tool (`fire_crawl_scrape`)

Scrape content from a single URL with advanced options.

```json
{
  "name": "fire_crawl_scrape",
  "arguments": {
    "url": "https://example.com",
    "formats": ["markdown"],
    "onlyMainContent": true,
    "waitFor": 1000,
    "timeout": 30000,
    "mobile": false,
    "includeTags": ["article", "main"],
    "excludeTags": ["nav", "footer"],
    "skipTlsVerification": false
  }
}
```

### 2. Batch Scrape Tool (`fire_crawl_batch_scrape`)

Scrape multiple URLs efficiently with built-in rate limiting and parallel processing.

```json
{
  "name": "fire_crawl_batch_scrape",
  "arguments": {
    "urls": ["https://example1.com", "https://example2.com"],
    "options": {
      "formats": ["markdown"],
      "onlyMainContent": true
    }
  }
}
```

### 3. Search Tool (`fire_crawl_search`)

Search the web and optionally extract content from search results.

```json
{
  "name": "fire_crawl_search",
  "arguments": {
    "query": "your search query",
    "limit": 5,
    "lang": "en",
    "country": "us",
    "scrapeOptions": {
      "formats": ["markdown"],
      "onlyMainContent": true
    }
  }
}
```

### 4. Crawl Tool (`fire_crawl_crawl`)

Start an asynchronous crawl with advanced options.

```json
{
  "name": "fire_crawl_crawl",
  "arguments": {
    "url": "https://example.com",
    "maxDepth": 2,
    "limit": 100,
    "allowExternalLinks": false,
    "deduplicateSimilarURLs": true
  }
}
```

### 5. Extract Tool (`fire_crawl_extract`)

Extract structured information from web pages using LLM capabilities.

```json
{
  "name": "fire_crawl_extract",
  "arguments": {
    "urls": ["https://example.com/page1"],
    "prompt": "Extract product information including name, price, and description",
    "systemPrompt": "You are a helpful assistant that extracts product information",
    "schema": {
      "type": "object",
      "properties": {
        "name": { "type": "string" },
        "price": { "type": "number" },
        "description": { "type": "string" }
      },
      "required": ["name", "price"]
    }
  }
}
```

## Development

```bash
# Install dependencies
npm install

# Build
npm run build

# Run tests
npm test
```

### Debugging

The server includes comprehensive logging. You can use the MCP inspector for debugging:

```bash
cd path/to/mcp-server-firecrawl
npx @modelcontextprotocol/inspector node build/index.js
```

View logs with:
```bash
tail -n 20 -f ~/Library/Logs/Claude/mcp*.log
```

## License

MIT License - see LICENSE file for details.

---
Note: This is a fork of the [original mcp-server-firecrawl repository](https://github.com/cawstudios/mcp-server-firecrawl).
