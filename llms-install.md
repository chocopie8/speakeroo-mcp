# Installing the Speakeroo MCP server

Speakeroo is a hosted remote MCP server. Nothing needs to be cloned, built or installed, and no API
key, environment variable or header is required.

- URL: `https://mcp.speakeroo.app/mcp`
- Transport: Streamable HTTP

## Steps

1. Add a remote server named `speakeroo` with the URL above to the client's MCP settings. For a
   client that uses an `mcpServers` file:

   ```json
   {
     "mcpServers": {
       "speakeroo": {
         "url": "https://mcp.speakeroo.app/mcp"
       }
     }
   }
   ```

   Cline: add the same entry to `cline_mcp_settings.json` with `"type": "streamableHttp"`.

2. If the client can only start local (stdio) servers, use the bridge instead:

   ```json
   {
     "mcpServers": {
       "speakeroo": {
         "command": "npx",
         "args": ["-y", "mcp-remote", "https://mcp.speakeroo.app/mcp"]
       }
     }
   }
   ```

3. Check it works: call `list_filler_words` (no arguments). It returns filler words grouped by
   what they do. Then try `analyze_transcript` with 30 to 1,500 words of a speech.

Free limits per network: 5 text analyses and 1 spoken analysis a day.
