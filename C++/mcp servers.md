1. # Filesystem MCP Server
```bash
git clone https://github.com/cyanheads/filesystem-mcp-server.git
cd filesystem-mcp-server
```

```bash
npm install
```

```bash
npm run build
```

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "node",
      "args": ["/path/to/filesystem-mcp-server/dist/index.js"],
      "env": {
        "FS_BASE_DIRECTORY": "/path/to/base/directory",
        "LOG_LEVEL": "debug"
      },
      "disabled": false,
      "autoApprove": []
    }
    // ... other servers
  }
}
```

