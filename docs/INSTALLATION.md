# Installation Guide

This guide provides detailed instructions for installing and configuring Azure Cosmos DB Shell.

## Prerequisites

- **VS Code** version 1.103 or later (for MCP auto-start support)
- **Azure Account** with access to Azure Cosmos DB resources
- **.NET SDK 9.0.301** or later (only if building from source)

## Downloads

Download from the [GitHub Releases page](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/tag/cosmosdb-shell-preview):

| Component | Windows | macOS | Linux |
|-----------|---------|-------|-------|
| **VS Code Extension** | [vscode-cosmosdb-0.31.1.vsix](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/vscode-cosmosdb-0.31.1.vsix) | ⬅️ Same file | ⬅️ Same file |
| **Cosmos Shell** | [cosmos_shell_win-x64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_win-x64.zip) | [cosmos_shell_osx-x64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_osx-x64.zip) | [cosmos_shell_linux-x64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_linux-x64.zip) |
| **Cosmos Shell (ARM)** | — | — | [cosmos_shell_linux-arm64.zip](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/download/cosmosdb-shell-preview/cosmos_shell_linux-arm64.zip) |

> 📁 [Browse all downloads](https://github.com/AzureCosmosDB/cosmosdb-shell-preview/releases/tag/cosmosdb-shell-preview)

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

## Step 2: Download and Extract Cosmos Shell

1. Download `CosmosShell.zip` for your platform
2. Extract the archive to a permanent location:

### Windows

```powershell
# Example: Extract to Program Files
Expand-Archive -Path "CosmosShell.zip" -DestinationPath "C:\Program Files\CosmosShell"
```

The executable will be at: `C:\Program Files\CosmosShell\win-x64\CosmosShell.exe`

### macOS

```bash
# Example: Extract to Applications
unzip CosmosShell.zip -d ~/Applications/CosmosShell
```

The executable will be at: `~/Applications/CosmosShell/osx-x64/CosmosShell`

### Linux

```bash
# Example: Extract to opt
sudo unzip CosmosShell.zip -d /opt/CosmosShell
```

The executable will be at: `/opt/CosmosShell/linux-x64/CosmosShell`

---

## Step 3: Set Executable Permissions (macOS/Linux)

### Linux

Make the shell executable:

```bash
chmod +x /opt/CosmosShell/linux-x64/CosmosShell
```

### macOS

Make the shell executable and remove the quarantine attribute:

```bash
# Make executable
chmod +x ~/Applications/CosmosShell/osx-x64/CosmosShell

# Remove quarantine (REQUIRED - macOS will refuse to run otherwise)
xattr -d com.apple.quarantine ~/Applications/CosmosShell/osx-x64/CosmosShell
```

> ⚠️ **Important:** The `xattr` command is required because macOS quarantines files downloaded from the internet. Without this step, you'll see a security warning and the shell won't run.

If you see "xattr: No such xattr: com.apple.quarantine", the file wasn't quarantined and you can proceed.

---

## Step 4: Configure VS Code Settings

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
    "cosmosDB.shell.path": "C:\\Program Files\\CosmosShell\\win-x64\\CosmosShell.exe"
}
```

**macOS:**
```json
{
    "cosmosDB.shell.path": "/Users/yourname/Applications/CosmosShell/osx-x64/CosmosShell"
}
```

**Linux:**
```json
{
    "cosmosDB.shell.path": "/opt/CosmosShell/linux-x64/CosmosShell"
}
```

> ⚠️ **Note:** Use the full absolute path to the executable. On Windows, use double backslashes (`\\`) or forward slashes (`/`) in the path.

---

## Step 5: Verify Installation

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

### "Permission denied" (Linux/macOS)

Run `chmod +x` on the shell executable:
```bash
chmod +x /path/to/CosmosShell
```

### "Cannot be opened because it is from an unidentified developer" (macOS)

Run the `xattr` command to remove the quarantine flag:
```bash
xattr -d com.apple.quarantine /path/to/CosmosShell
```

### "The specified executable is not valid"

- Verify you downloaded the correct platform version
- Ensure the file isn't corrupted (re-download if necessary)
- Check that you extracted all files from the archive

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

Delete the extracted folder containing the shell executable.

### Remove Settings

Remove the `cosmosDB.shell.path` entry from your `settings.json`.
