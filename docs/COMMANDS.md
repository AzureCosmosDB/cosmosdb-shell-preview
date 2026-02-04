# Command Reference

Complete reference for all Cosmos Shell commands.

## Navigation Commands

### `connect`

Connect to a Cosmos DB endpoint.

```
Usage: connect [-hint <ARG>] connectionString

Arguments:
    connectionString    The account connection string or endpoint URL

Options:
    -hint              Pre-populates the username for Azure AD login
```

**Examples:**
```bash
# Connect using endpoint (Azure AD auth)
connect "https://myaccount.documents.azure.com:443/"

# Connect with username hint
connect -hint "user@contoso.com" "https://myaccount.documents.azure.com:443/"
```

---

### `azconnect`

Alternative connect command using Azure authentication.

```
Usage: azconnect endpoint

Arguments:
    endpoint    The account endpoint or connection string
```

---

### `disconnect`

Disconnect from the current account.

```
Usage: disconnect
```

---

### `cd`

Change scope to a database or container.

```
Usage: cd [item]

Arguments:
    [item]    The database or container to select (optional)
```

**Behavior by context:**
- From connected state: `cd <database>` enters that database
- From database: `cd <container>` enters that container
- `cd ..` goes up one level
- `cd` (no argument) returns to connected state
- Supports chaining: `cd dbName/containerName`

**Examples:**
```bash
# Enter a database
cd myDatabase

# Enter a container
cd myContainer

# Chain navigation
cd myDatabase/myContainer

# Go up one level
cd ..

# Switch containers quickly
cd ../otherContainer

# Return to connected state
cd
```

---

### `ls`

List resources at the current scope.

```
Usage: ls [-m <ARG>] [filter]

Arguments:
    [filter]    Filter pattern (optional)

Options:
    -max, -m    Maximum number of items to return
```

**Behavior by context:**
- Connected state: Lists databases
- In database: Lists containers
- In container: Lists items (documents)

**Examples:**
```bash
# List all databases
ls

# List with limit
ls -m 10

# Filter results
ls "user*"
```

---

## Create Commands

### `mkdb`

Create a new database.

```
Usage: mkdb name

Arguments:
    name    The database name to create
```

**Example:**
```bash
mkdb myNewDatabase
```

---

### `mkcon`

Create a new container in the current database.

```
Usage: mkcon name partition_key

Arguments:
    name            The container name
    partition_key   The partition key path
```

**Example:**
```bash
cd myDatabase
mkcon users /userId
```

---

### `mkitem`

Create items in the current container. Reads JSON from the pipeline.

```
Usage: mkitem
```

**Examples:**
```bash
# Create a single item
echo '{"id":"1","pk":"a","name":"Item 1"}' | mkitem

# Create multiple items
echo '[{"id":"1","pk":"a"},{"id":"2","pk":"b"}]' | mkitem
```

---

### `create`

Generic create command for items, containers, or databases.

```
Usage: create item [name] [partition_key]

Arguments:
    item            Object type: item, container, or database
    [name]          Container or database name (optional)
    [partition_key] Partition key for container (optional)
```

---

## Delete Commands

### `rm`

Remove items from the current container.

```
Usage: rm pattern

Arguments:
    pattern    Pattern for items to remove
```

**Example:**
```bash
rm item123
```

---

### `rmcon`

Remove a container.

```
Usage: rmcon name

Arguments:
    name    The container to remove
```

**Example:**
```bash
rmcon oldContainer
```

---

### `rmdb`

Remove a database.

```
Usage: rmdb name

Arguments:
    name    The database to remove
```

**Example:**
```bash
rmdb oldDatabase
```

---

### `delete`

Generic delete command.

```
Usage: delete item pattern

Arguments:
    item      Object type: item, container, or database
    pattern   The items/container/database to delete
```

---

## Query Commands

### `query`

Execute a SQL query and return results.

```
Usage: query [-m <ARG>] query

Arguments:
    query    The SQL query to execute

Options:
    -max, -m    Maximum number of items to return
```

**Examples:**
```bash
# Basic query
query "SELECT * FROM c"

# Query with filter
query "SELECT * FROM c WHERE c.status = 'active'"

# Limit results
query -m 10 "SELECT * FROM c ORDER BY c._ts DESC"

# Query specific fields
query "SELECT c.id, c.name FROM c WHERE c.type = 'user'"
```

---

### `print`

Print a specific item by ID and partition key.

```
Usage: print id key

Arguments:
    id     The ID of the item
    key    The partition key value
```

**Example:**
```bash
print user123 "partition-a"
```

---

## Utility Commands

### `echo`

Print a message or pipe JSON to other commands.

```
Usage: echo message

Arguments:
    message    The message or JSON to print
```

**Examples:**
```bash
# Print a message
echo "Hello, Cosmos!"

# Create JSON for piping
echo '{"id":"1","pk":"a"}' | mkitem

# Print from pipeline
ls | echo $.items[0]
```

---

### `cat`

Display the contents of a file.

```
Usage: cat [path]

Arguments:
    [path]    The file path to display (optional)
```

---

### `settings`

Show account overview or container settings.

```
Usage: settings
```

**Behavior:**
- Connected/database state: Shows account overview
- Container state: Shows container settings

---

### `bucket`

Get or set the SDK throughput bucket.

```
Usage: bucket [bucket]

Arguments:
    [bucket]    Bucket number (0 clears; valid: 1-5) (optional)
```

---

### `help`

Show help for commands.

```
Usage: help [-details] [command]

Arguments:
    [command]    Specific command to get help for (optional)

Options:
    -details, -d    Show detailed help for all commands
```

**Examples:**
```bash
# List all commands
help

# Get help for specific command
help query

# Show detailed help for all commands
help -details
```

---

### `exit`

Exit the Cosmos Shell.

```
Usage: exit
```

---

## External Tool Commands

### `az`

Execute Azure CLI commands.

```
Usage: az [args]

Arguments:
    [args]    Arguments to pass to az (optional)
```

**Example:**
```bash
az account show
```

---

### `jq`

Process JSON using jq.

```
Usage: jq [args]

Arguments:
    [args]    Arguments to pass to jq (optional)
```

**Example:**
```bash
query "SELECT * FROM c" | jq '.Documents[0]'
```

---

### `jtab`

Process JSON for tabular display.

```
Usage: jtab args

Arguments:
    args    Arguments to pass to jtab
```

---

## Using Pipes and JSON Paths

The `|` operator pipes JSON results between commands.

### JSON Path Syntax

Access values from piped JSON using paths starting with `$`:

- `$` - The entire JSON result
- `$.property` - Access a property
- `$.[0]` - Access array element by index
- `$.items[0].id` - Chain access

### Examples

```bash
# Enter first database from list
ls | cd $.[0]

# Get first item's ID
ls -m 1 | echo $.items[0].id

# Create items from array
echo '[{"id":"a"},{"id":"b"}]' | mkitem

# Extract nested property
echo '{"user":{"name":"Ada"}}' | echo $.user.name

# Query and process result
query "SELECT * FROM c" | echo $.Documents[0].id
```

---

## Command Line Arguments

When starting Cosmos Shell from the command line:

| Argument | Description |
|----------|-------------|
| `--cs <value>` | Color system (0=off, 1=standard, 2=true color) |
| `-c <command>` | Execute command and exit |
| `-k <command>` | Execute command and keep running |
| `--clearhistory` | Clear command history and exit |
| `--connect <string>` | Connection string to connect on startup |
| `--mcp` | Enable MCP server |
| `--mcp-port <port>` | MCP server port (0 for stdio) |
| `--help` | Display help |
| `--version` | Display version |

**Examples:**
```bash
# Start with connection
CosmosShell --connect "https://myaccount.documents.azure.com:443/"

# Execute command and exit
CosmosShell -c "connect 'endpoint'; ls"

# Start with MCP enabled
CosmosShell --mcp --mcp-port 6128
```

---

## Next Steps

- [Scripting Guide](SCRIPTING.md) - Write scripts and custom commands
- [MCP Server Guide](MCP-SERVER.md) - Set up AI assistant integration
