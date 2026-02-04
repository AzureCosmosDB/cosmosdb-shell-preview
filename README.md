# Azure Cosmos DB Shell (Private Preview)

> ⚠️ **Private Preview** - This is a pre-release version for testing and feedback purposes only.

Azure Cosmos DB Shell is a powerful command-line interface for Azure Cosmos DB that enables you to interact with your databases using intuitive bash-like commands. It features optional MCP (Model Context Protocol) server support for AI-powered automation.

![Cosmos DB Shell](assets/cosmos-shell-banner.png)

## ✨ Features

- **Bash-like Navigation** - Use familiar `ls` and `cd` commands to explore databases and containers
- **Full CRUD Operations** - Create, read, query, update, and delete resources with simple commands
- **Scripting Support** - Write scripts with variables, control flow, and custom functions
- **Pipeline Composition** - Chain commands using pipes (`|`) with JSON data flow
- **MCP Server Integration** - Enable AI assistants to interact with your Cosmos DB resources
- **VS Code Integration** - Launch directly from the Azure extension sidebar

## 📋 Table of Contents

- [Quick Start](#-quick-start)
- [Installation](#-installation)
  - [VS Code Extension](#1-install-the-vs-code-extension)
  - [Cosmos Shell Setup](#2-download-and-configure-cosmos-shell)
- [MCP Server Setup](#-mcp-server-setup-optional)
- [Usage Guide](#-usage-guide)
- [Documentation](#-documentation)
- [Security](#-security-considerations)
- [Troubleshooting](#-troubleshooting)
- [Feedback & Support](#-feedback--support)

## 🚀 Quick Start

1. Download the [VS Code extension and Cosmos Shell binaries](#-downloads)
2. Install the extension and configure the shell path
3. Connect to your Azure account in VS Code
4. Right-click a Cosmos DB resource and select **"Launch Cosmos Shell"**
5. Start exploring with `ls`, `cd`, and `query` commands!

## 📥 Downloads

Download from the [GitHub Releases page](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/tag/cosmosdb-shell-preview):

| Component | Windows | macOS | Linux |
|-----------|---------|-------|-------|
| **VS Code Extension** | [vscode-cosmosdb-0.31.1.vsix](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/vscode-cosmosdb-0.31.1.vsix) | ⬅️ Same file | ⬅️ Same file |
| **Cosmos Shell** | [cosmos_shell_win-x64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_win-x64.zip) | [cosmos_shell_osx-x64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_osx-x64.zip) | [cosmos_shell_linux-x64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_linux-x64.zip) |
| **Cosmos Shell (ARM)** | — | — | [cosmos_shell_linux-arm64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_linux-arm64.zip) |

> 📁 [Browse all downloads](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/tag/cosmosdb-shell-preview)

---

## 📦 Installation

### 1. Install the VS Code Extension

1. Download `vscode-cosmosdb-0.31.1.vsix` for your OS from the [Downloads](#-downloads) section
2. Open **VS Code**
3. Open the **Extensions** sidebar (`Ctrl+Shift+X` / `Cmd+Shift+X`)
4. Click the `...` menu (top-right) → **Install from VSIX...**
5. Select the downloaded `.vsix` file (or drag & drop it into the Extensions sidebar)
6. **Restart VS Code**

### 2. Download and Configure Cosmos Shell

#### Step 1: Extract the Shell

1. Download `CosmosShell.zip` for your platform from [Downloads](#-downloads)
2. Extract the archive
3. Navigate to the subfolder for your platform:
   - `win-x64` for Windows
   - `osx-x64` for macOS
   - `linux-x64` for Linux

#### Step 2: Set Executable Permissions (macOS/Linux only)

**Linux:**
```bash
chmod +x "/path/to/CosmosShell"
```

**macOS:**
```bash
chmod +x "/path/to/CosmosShell"
xattr -d com.apple.quarantine "/path/to/CosmosShell"
```

> ⚠️ The `xattr` command removes the quarantine flag that macOS applies to downloaded files.

#### Step 3: Configure VS Code Settings

1. Open VS Code Settings (`Ctrl+,` / `Cmd+,`)
2. Search for `cosmosDB.shell.path`
3. Click **"Edit in settings.json"**
4. Add the full path to your CosmosShell executable:

**Windows:**
```json
{
    "cosmosDB.shell.path": "C:\\path\\to\\CosmosShell.exe"
}
```

**macOS/Linux:**
```json
{
    "cosmosDB.shell.path": "/path/to/CosmosShell"
}
```

---

## 🤖 MCP Server Setup (Optional)

The MCP (Model Context Protocol) server allows AI assistants and compatible clients to interact with Cosmos DB through the shell.

### Enable MCP in Settings

1. Open VS Code Settings
2. Set `cosmosDB.shell.MCP.enabled` to `true`
3. (Optional) Configure `cosmosDB.shell.MCP.port` (default: `6128`)

### Configure MCP in VS Code

#### Option A: Auto-Start (VS Code 1.103+)

1. Open Settings and search for `chat.mcp.autostart`
2. Select `newAndOutdated` to auto-start MCP servers

#### Option B: Manual Configuration

Add `.vscode/mcp.json` to your workspace:

```json
{
    "servers": {
        "localCosmosShellServer": {
            "url": "http://localhost:6128"
        }
    }
}
```

Or use Command Palette → **MCP: Add Server...** → HTTP → `http://localhost:6128`

### Start the MCP Server

1. Launch Cosmos Shell using the **"Launch Cosmos Shell"** command
2. If auto-start is disabled:
   - Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
   - Run **MCP: List Servers**
   - Select `localCosmosShellServer` → **Start Server**

### Verify It's Running

Check the **Output** tab in VS Code for server startup messages.

> ⚠️ See [Security Considerations](docs/SECURITY.md) before enabling MCP.

---

## 📖 Usage Guide

### Connect to Your Account

1. Open VS Code and the **Azure** sidebar
2. Sign in to your Azure account
3. Expand your subscription → **Azure Cosmos DB**
4. Right-click a database or container → **"Launch Cosmos Shell"**

Or use Command Palette → **NoSQL: Launch Cosmos Shell**

### Basic Navigation

```bash
# Connect to an endpoint
connect "https://your-account.documents.azure.com:443/"

# List databases
ls

# Enter a database
cd myDatabase

# List containers
ls

# Enter a container
cd myContainer

# List items (documents)
ls

# Go up one level
cd ..

# Return to connected state
cd
```

### Common Operations

```bash
# Query items
query "SELECT * FROM c WHERE c.status = 'active'"

# Create a database
mkdb newDatabase

# Create a container
mkcon newContainer /partitionKey

# Create items
echo '[{"id":"1","pk":"a"},{"id":"2","pk":"b"}]' | mkitem

# Delete an item
rm itemId

# View item details
print itemId partitionKey
```

### Using Pipes

```bash
# Enter the first database
ls | cd $.[0]

# Get the first item's ID
ls -m 1 | echo $.items[0].id

# Chain operations
query "SELECT * FROM c" | echo $.Documents[0]
```

### Get Help

```bash
# List all commands
help

# Get help for a specific command
help query
```

---

## 📚 Documentation

| Document | Description |
|----------|-------------|
| [Installation Guide](docs/INSTALLATION.md) | Detailed installation instructions |
| [MCP Server Guide](docs/MCP-SERVER.md) | Complete MCP setup and configuration |
| [Command Reference](docs/COMMANDS.md) | Full list of available commands |
| [Scripting Guide](docs/SCRIPTING.md) | Variables, control flow, and custom functions |
| [Security Guide](docs/SECURITY.md) | Security considerations and best practices |

---

## 🔒 Security Considerations

> ⚠️ **Read the [full security guide](docs/SECURITY.md) before enabling MCP.**

Key points:
- MCP server runs locally and executes commands with your user permissions
- Connected MCP clients can create, read, update, and delete resources
- Your MCP client may transmit data to remote LLM services
- **Keep MCP bound to localhost** and restrict firewall access
- Prefer Azure AD/managed identity over account keys
- Review and approve tool requests in the MCP client UI

---

## 🔧 Troubleshooting

### Shell won't start

- Verify `cosmosDB.shell.path` points to the correct executable
- On macOS: ensure you ran the `xattr` command to remove quarantine
- On Linux/macOS: ensure the file has execute permissions (`chmod +x`)

### Authentication fails

- Sign out and sign back in to Azure in VS Code
- Check that your account has access to the Cosmos DB resource
- Try using a connection string instead of Azure AD

### MCP server not connecting

- Ensure Cosmos Shell is running (use "Launch Cosmos Shell" command)
- Verify the port matches your `cosmosDB.shell.MCP.port` setting
- Check the Output panel for error messages
- Ensure no firewall is blocking localhost:6128

### Commands not recognized

- Type `help` to see available commands
- Check you're in the correct scope (connected/database/container)

---

## 💬 Feedback & Support

This is a **private preview**. Your feedback helps us improve!

- **Report Issues:** [Create an issue](https://github.com/your-org/cosmos-db-shell-preview/issues)
- **Feature Requests:** [Submit feedback](https://github.com/your-org/cosmos-db-shell-preview/discussions)
- **Questions:** Reach out to the Cosmos DB team

---

## 📄 License

This software is provided under a preview license for testing purposes only. See [LICENSE](LICENSE) for details.

---

<p align="center">
  <strong>Azure Cosmos DB Shell</strong> • Private Preview
</p>
