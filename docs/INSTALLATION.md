# Installation Guide

This guide provides detailed instructions for installing and configuring Azure Cosmos DB Shell.

## Prerequisites

- **VS Code** version 1.103 or later (for MCP auto-start support)
- **Azure Account** with access to Azure Cosmos DB resources
- **.NET SDK 9.0.301** or later (required to install the shell as a .NET global tool)

## Downloads

The **Cosmos Shell** is distributed as a .NET global tool on NuGet — no manual downloads or extraction required. See [Step 2](#step-2-install-cosmos-shell-from-nuget) below.

The **VS Code Extension** is available from the [GitHub Releases page](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/tag/cosmosdb-shell-preview):

| Component | Download |
|-----------|----------|
| **VS Code Extension** | [vscode-cosmosdb-0.31.1.vsix](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/vscode-cosmosdb-0.31.1.vsix) |

---

## Step 1: Install the VS Code Extension

### Method A: Install via VSIX Menu

1. Download `vscode-cosmosdb-0.31.1.vsix` from the links above
2. Open **VS Code**
3. Open the **Extensions** sidebar:
   - Press `Ctrl+Shift+X` (Windows/Linux) or `Cmd+Shift+X` (macOS)
   - Or click the Extensions icon in the Activity Bar
4. Click the **"..."** menu button in the top-right corner
5. Select **"Install from VSIX..."**
6. Navigate to and select the downloaded `.vsix` file
7. Wait for the installation to complete
8. **Restart VS Code** when prompted

### Method B: Drag and Drop

1. Download `vscode-cosmosdb-0.31.1.vsix`
2. Open VS Code with the Extensions sidebar visible
3. Drag the `.vsix` file and drop it into the Extensions sidebar
4. Confirm the installation
5. **Restart VS Code**

### Method C: Command Line

```bash
code --install-extension /path/to/vscode-cosmosdb-0.31.1.vsix
```

Then restart VS Code.

---

## Step 2: Install Cosmos Shell from NuGet

Cosmos Shell is published as a .NET global tool. Install it from any terminal:

```bash
dotnet tool install --global CosmosDBShell --prerelease
```

This installs the `CosmosDBShell` command and makes it available on your `PATH` for all platforms (Windows, macOS, Linux — including ARM). No manual download, extraction, or `chmod`/`xattr` steps are required.

### Verify the installation

```bash
CosmosDBShell --version
```

### Upgrade later

```bash
dotnet tool update --global CosmosDBShell --prerelease
```

> 💡 If `CosmosDBShell` is not found after install, ensure the .NET tools directory is on your `PATH` (typically `%USERPROFILE%\.dotnet\tools` on Windows or `~/.dotnet/tools` on macOS/Linux).

### Alternative: Self-contained ZIP downloads (no .NET SDK required)

If you can't or don't want to use NuGet / `dotnet tool`, self-contained preview builds are published as ZIP files on the [`Azure/CosmosDBShell` releases page](https://github.com/Azure/CosmosDBShell/releases/tag/1.0-preview). These bundles include the .NET runtime, so no .NET SDK install is needed.

1. Download the ZIP for your platform (Windows, macOS, or Linux — including ARM) from the [1.0-preview release](https://github.com/Azure/CosmosDBShell/releases/tag/1.0-preview).
2. Extract the archive to a permanent location.
3. **macOS/Linux only** — mark the executable as runnable, and on macOS remove the quarantine attribute:

   ```bash
   chmod +x /path/to/CosmosDBShell
   # macOS only:
   xattr -d com.apple.quarantine /path/to/CosmosDBShell
   ```

4. When configuring VS Code in [Step 3](#step-3-configure-vs-code-settings), set `cosmosDB.shell.path` to the **full absolute path** of the extracted executable instead of the short command name.

---

## Step 3: Configure VS Code Settings

You need to tell VS Code where to find the Cosmos Shell executable.

### Method A: Settings UI

1. Open VS Code Settings:
   - Press `Ctrl+,` (Windows/Linux) or `Cmd+,` (macOS)
   - Or go to **File** → **Preferences** → **Settings**
2. Search for `cosmosDB.shell.path`
3. Click **"Edit in settings.json"**
4. Add the path to your executable

### Method B: Edit settings.json Directly

1. Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Type "Open User Settings (JSON)" and select it
3. Add the following setting:

**Windows:**
```json
{
    "cosmosDB.shell.path": "CosmosDBShell.exe"
}
```

**macOS/Linux:**
```json
{
    "cosmosDB.shell.path": "CosmosDBShell"
}
```

> 💡 Since `CosmosDBShell` is installed as a .NET global tool and available on your `PATH`, you can simply use the command name. If your `PATH` isn't picked up by VS Code, use the full absolute path instead (e.g., `%USERPROFILE%\\.dotnet\\tools\\CosmosDBShell.exe` on Windows or `~/.dotnet/tools/CosmosDBShell` on macOS/Linux).

---

## Step 4: Verify Installation

1. Open VS Code
2. Open the **Azure** sidebar (click the Azure icon in the Activity Bar)
3. Sign in to your Azure account if not already signed in
4. Expand your subscription and find **Azure Cosmos DB**
5. Expand a Cosmos DB account
6. Right-click on a database or container
7. Select **"Launch Cosmos Shell"**

The terminal should open with the Cosmos Shell connected to your selected resource.

### Alternative: Launch via Command Palette

1. Press `Ctrl+Shift+P` / `Cmd+Shift+P`
2. Type "NoSQL: Launch Cosmos Shell"
3. Select the command

---

## Troubleshooting Installation Issues

### "Shell path not configured"

Ensure `cosmosDB.shell.path` is set in your VS Code settings and points to the correct executable.

### `CosmosDBShell` command not found

Ensure the .NET global tools folder is on your `PATH`:

- **Windows:** `%USERPROFILE%\.dotnet\tools`
- **macOS/Linux:** `~/.dotnet/tools`

Restart your terminal (and VS Code) after installing the tool so the updated `PATH` is picked up.

### `dotnet` command not found

Install the [.NET SDK 9.0.301 or later](https://dotnet.microsoft.com/download), then re-run:

```bash
dotnet tool install --global CosmosDBShell --prerelease
```

### VS Code extension doesn't appear after installation

- Completely close and reopen VS Code
- Check the Extensions sidebar for any error messages
- Try uninstalling any existing Cosmos DB extension first

---

## Next Steps

- [Configure MCP Server](MCP-SERVER.md) (optional) - Enable AI assistant integration
- [Command Reference](COMMANDS.md) - Learn available commands
- [Usage Guide](../README.md#-usage-guide) - Start using the shell

---

## Uninstallation

### Remove the VS Code Extension

1. Open Extensions sidebar
2. Find "Azure Databases" extension
3. Click **Uninstall**

### Remove Cosmos Shell

Uninstall the .NET global tool:

```bash
dotnet tool uninstall --global CosmosDBShell
```

### Remove Settings

Remove the `cosmosDB.shell.path` entry from your `settings.json`.
