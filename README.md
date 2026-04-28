# Azure Cosmos DB Shell (Private Preview)

> ⚠️ **Private Preview** - This is a pre-release version for testing and feedback purposes only.

Azure Cosmos DB Shell is a powerful command-line interface for Azure Cosmos DB that enables you to interact with your databases using intuitive bash-like commands. It features optional MCP (Model Context Protocol) server support for AI-powered automation.

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
  - [Cosmos Shell Setup](#2-install-cosmos-shell-from-nuget)
- [MCP Server Setup](#-mcp-server-setup-optional)
- [Usage Guide](#-usage-guide)
- [Documentation](#-documentation)
- [Security](#-security-considerations)
- [Troubleshooting](#-troubleshooting)
- [Feedback & Support](#-feedback--support)

## 🚀 Quick Start

1. Install the [VS Code extension](#1-install-the-vs-code-extension)
2. Install Cosmos Shell from NuGet: `dotnet tool install --global CosmosDBShell --prerelease`
3. Connect to your Azure account in VS Code
4. Right-click a Cosmos DB resource and select **"Launch Cosmos Shell"**
5. Start exploring with `ls`, `cd`, and `query` commands!

## 📥 Downloads

The latest preview builds are available from the [cosmosdb-shell-preview-1.0.213 GitHub release](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/tag/cosmosdb-shell-preview-1.0.213). The release contains ZIP archives with the self-contained shell executable and the latest VS Code extension VSIX.

| Component | Platform | Version | Download |
|-----------|----------|---------|----------|
| **Cosmos Shell ZIP** | Windows x64 | 1.0.213-preview | [cosmos_shell_win-x64_1.0.213-preview.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview-1.0.213/cosmos_shell_win-x64_1.0.213-preview.zip) |
| **Cosmos Shell ZIP** | macOS x64 | 1.0.213-preview | [cosmos_shell_osx-x64_1.0.213-preview.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview-1.0.213/cosmos_shell_osx-x64_1.0.213-preview.zip) |
| **Cosmos Shell ZIP** | macOS arm64 | 1.0.213-preview | [cosmos_shell_osx-arm64_1.0.213-preview.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview-1.0.213/cosmos_shell_osx-arm64_1.0.213-preview.zip) |
| **Cosmos Shell ZIP** | Linux x64 | 1.0.213-preview | [cosmos_shell_linux-x64_1.0.213-preview.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview-1.0.213/cosmos_shell_linux-x64_1.0.213-preview.zip) |
| **Cosmos Shell ZIP** | Linux arm64 | 1.0.213-preview | [cosmos_shell_linux-arm64_1.0.213-preview.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview-1.0.213/cosmos_shell_linux-arm64_1.0.213-preview.zip) |
| **VS Code Extension** | All platforms | 0.33.3 | [vscode-cosmosdb-0.33.3.vsix](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview-1.0.213/vscode-cosmosdb-0.33.3.vsix) |

---

## 📦 Installation

### 1. Install the VS Code Extension

1. Download `vscode-cosmosdb-0.33.3.vsix` from the [Downloads](#-downloads) section
2. Open **VS Code**
3. Open the **Extensions** sidebar (`Ctrl+Shift+X` / `Cmd+Shift+X`)
4. Click the `...` menu (top-right) → **Install from VSIX...**
5. Select the downloaded `.vsix` file (or drag & drop it into the Extensions sidebar)
6. **Restart VS Code**

### 2. Install Cosmos Shell from NuGet

Cosmos Shell is published as a .NET global tool. Install it from any terminal:

```bash
dotnet tool install --global CosmosDBShell --prerelease
```

This installs the `CosmosDBShell` command on Windows, macOS, and Linux (including ARM). No manual download, extraction, or permission changes are required.

> 💡 Requires the [.NET SDK 10.0](https://dotnet.microsoft.com/download) or later. Upgrade later with `dotnet tool update --global CosmosDBShell --prerelease`.

#### Alternative: Self-contained ZIP (no .NET SDK required)

Prefer not to use NuGet / `dotnet tool`? Self-contained preview builds (with the .NET runtime bundled) are published as ZIPs on the [cosmosdb-shell-preview-1.0.213 GitHub release](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/tag/cosmosdb-shell-preview-1.0.213). Download the archive for your platform and extract it. On macOS/Linux, run `chmod +x` on the binary (and `xattr -d com.apple.quarantine` on macOS).

##### Configure VS Code Settings

Only configure `cosmosDB.shell.path` if you are using a self-contained ZIP or VS Code cannot find the globally installed `CosmosDBShell` command.

1. Open VS Code Settings (`Ctrl+,` / `Cmd+,`)
2. Search for `cosmosDB.shell.path`
3. Click **"Edit in settings.json"**
4. Set the value to the full absolute path of the extracted executable:

**Windows:**
```json
{
    "cosmosDB.shell.path": "C:\\path\\to\\CosmosDBShell.exe"
}
```

**macOS/Linux:**
```json
{
    "cosmosDB.shell.path": "/path/to/CosmosDBShell"
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

- **Report Issues:** [Create an issue](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/issues)
- **Feature Requests:** [Submit feedback](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/discussions)
- **Questions:** Reach out to the Cosmos DB team

---

## 📄 License

This software is provided under a preview license for testing purposes only. See [LICENSE](LICENSE) for details.

---

<p align="center">
  <strong>Azure Cosmos DB Shell</strong> • Private Preview
</p>
