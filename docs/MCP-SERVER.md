# MCP Server Guide

The Model Context Protocol (MCP) server allows AI assistants and compatible clients to programmatically interact with Azure Cosmos DB through the shell.

> ⚠️ **Security Notice:** Before enabling MCP, please read the [Security Considerations](SECURITY.md).

## Overview

When MCP is enabled, Cosmos Shell exposes an HTTP server (default port 6128) that compatible clients can connect to. This allows:

- AI assistants to execute Cosmos DB operations
- Automated tools to interact with your databases
- Integration with VS Code's built-in MCP support

## Prerequisites

- Cosmos Shell installed and configured ([Installation Guide](INSTALLATION.md))
- VS Code version 1.103 or later (for auto-start support)

---

## Enabling MCP in Cosmos Shell

### Step 1: Enable MCP in VS Code Settings

1. Open VS Code Settings (`Ctrl+,` / `Cmd+,`)
2. Search for `cosmosDB.shell.MCP`
3. Configure the following settings:

| Setting | Description | Default |
|---------|-------------|---------|
| `cosmosDB.shell.MCP.enabled` | Enable/disable MCP server | `false` |
| `cosmosDB.shell.MCP.port` | Port for MCP server | `6128` |

Or add to your `settings.json`:

```json
{
    "cosmosDB.shell.MCP.enabled": true,
    "cosmosDB.shell.MCP.port": 6128
}
```

---

## Configuring VS Code to Connect to MCP

### Option A: Auto-Start (Recommended for VS Code 1.103+)

VS Code 1.103 introduced automatic MCP server management.

1. Open VS Code Settings
2. Search for `chat.mcp.autostart`
3. Set to `newAndOutdated` to automatically start MCP servers

```json
{
    "chat.mcp.autostart": "newAndOutdated"
}
```

You can also configure this from the refresh icon tooltip in the Chat view.

### Option B: Workspace Configuration (mcp.json)

Create a `.vscode/mcp.json` file in your workspace:

```json
{
    "servers": {
        "localCosmosShellServer": {
            "url": "http://localhost:6128"
        }
    }
}
```

### Option C: Add Server via Command Palette

1. Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Run **"MCP: Add Server..."**
3. Select **HTTP**
4. Enter URL: `http://localhost:6128`
5. Give it a name (e.g., "Cosmos Shell")
6. Complete the setup

---

## Starting the MCP Server

### Automatic Start (if auto-start is enabled)

If you've configured `chat.mcp.autostart`, the server will start automatically when needed.

### Manual Start

1. First, ensure Cosmos Shell is running:
   - Use the **"Launch Cosmos Shell"** command from the Azure sidebar
   - Or run **"NoSQL: Launch Cosmos Shell"** from Command Palette

2. Start the MCP server:
   - Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
   - Run **"MCP: List Servers"**
   - Select `localCosmosShellServer`
   - Click **Start Server**

### Command Line Start

You can also start Cosmos Shell with MCP from the command line:

```bash
# Windows
CosmosShell.exe --mcp --mcp-port 6128

# macOS/Linux
./CosmosShell --mcp --mcp-port 6128
```

---

## Verifying MCP is Running

### Check the Output Panel

1. In VS Code, go to **View** → **Output** (or `Ctrl+Shift+U`)
2. From the dropdown, select the MCP-related output
3. Look for messages confirming the server started successfully

### Check from Command Palette

1. Run **"MCP: List Servers"**
2. Verify `localCosmosShellServer` shows as running (green indicator)

### Test the Connection

The MCP server exposes tools that AI assistants can call. In VS Code Chat, you should see Cosmos DB tools available when the server is running.

---

## MCP Server Configuration

### Command Line Arguments

| Argument | Description |
|----------|-------------|
| `--mcp` | Enable MCP server |
| `--mcp-port <port>` | Set MCP port (0 for stdio mode) |

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `COSMOS_SHELL_CSVSEP` | CSV separator character | `;` |

---

## Using MCP with AI Assistants

Once MCP is running, compatible AI assistants can:

- **Query databases** - Execute queries and retrieve results
- **Navigate resources** - List databases, containers, and items
- **Manage data** - Create, update, and delete items
- **Run scripts** - Execute shell scripts and custom commands

### Example Interactions

With an MCP-enabled AI assistant, you can ask natural language questions like:

- "List all databases in my Cosmos DB account"
- "Query the users container for active users"
- "Create a new item in the orders container"
- "Show me the settings for the products container"

---

## Troubleshooting

### Server won't start

1. Ensure Cosmos Shell is installed and path is configured correctly
2. Check that the port isn't already in use:
   ```bash
   # Windows
   netstat -ano | findstr :6128
   
   # macOS/Linux
   lsof -i :6128
   ```
3. Try a different port in settings

### Can't connect to server

1. Verify the server is running (check Output panel)
2. Ensure firewall isn't blocking localhost:6128
3. Confirm the URL in mcp.json matches your port setting

### Tools not appearing in Chat

1. Restart the MCP server
2. Refresh the Chat view
3. Check MCP: List Servers for any errors

### Authentication issues

The MCP server uses the same authentication as the shell. Ensure you're signed into Azure in VS Code before starting the shell.

---

## Disabling MCP

To disable MCP:

1. Set `cosmosDB.shell.MCP.enabled` to `false` in settings
2. Or remove the `--mcp` flag if starting from command line
3. Restart Cosmos Shell

---

## Security Best Practices

See the full [Security Guide](SECURITY.md) for detailed information.

Quick checklist:
- ✅ Keep MCP bound to localhost only
- ✅ Use Azure AD authentication instead of connection strings
- ✅ Review and approve tool requests in the MCP client
- ✅ Don't auto-approve destructive operations
- ✅ Disable MCP when not in use

---

## Next Steps

- [Security Considerations](SECURITY.md) - Important security information
- [Command Reference](COMMANDS.md) - Full list of shell commands
- [Scripting Guide](SCRIPTING.md) - Write scripts and custom commands
