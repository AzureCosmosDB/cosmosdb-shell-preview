# Bug Bash Scenarios

Use these scenarios to test Cosmos DB Shell from an end-user perspective. A normal Cosmos DB account is fine; create clearly named test databases, containers, and items so cleanup is easy. Record the command used, observed result, expected result, OS, terminal, authentication mode, and any logs or screenshots for each issue.

## Setup

- Install or build the latest shell.
- Verify `cosmosdbshell --version` prints the product, version, and commit in the expected format.
- Use a normal Cosmos DB account or the emulator.
- Prepare at least one clearly named test database and container with a known partition key.
- Use test data only. Do not run destructive commands against existing production databases, containers, or items.

## Scenarios

### 1. First Launch And Help

Goal: Confirm a new user can discover how to start.

Steps:

1. Run `cosmosdbshell --help`.
2. Run `cosmosdbshell --version`.
3. Start `cosmosdbshell` without arguments.
4. Try an unknown command.
5. Exit the shell.

Expected:

- Help text is readable and lists common options.
- Version output is concise and does not show duplicated commit metadata.
- The interactive shell starts without errors.
- Unknown commands produce actionable errors without crashing.

### 2. Connect With A Connection String

Goal: Validate the simplest connection flow.

Steps:

1. Start the shell.
2. Run `connect "<connection-string>"`.
3. Run `pwd`.
4. Run `ls`.
5. Run `disconnect`.
6. Run `pwd` again.

Expected:

- Successful connection shows the root account scope.
- `ls` lists databases or clearly reports that none exist.
- Disconnect returns the shell to the disconnected state.

### 3. Connect With Entra ID

Goal: Validate interactive identity authentication.

Steps:

1. Run `connect "https://<account>.documents.azure.com:443/"`.
2. Run `ls`.
3. Run a simple navigation command such as `cd <database>`.

Expected:

- Login prompt uses the provided tenant or hint when specified.
- Authorized users can list and navigate resources.
- Authentication or permission failures are explained clearly.

### 4. Navigation Across Account, Database, And Container

Goal: Confirm users can move through Cosmos DB scopes reliably.

Steps:

1. Connect to an account with at least one database and container.
2. Run `pwd`.
3. Run `ls`.
4. Run `cd <database>`.
5. Run `pwd` and `ls`.
6. Run `cd <container>`.
7. Run `pwd` and `ls -m 5`.
8. Run `cd ..` twice.

Expected:

- `pwd` always matches the current location.
- `ls` changes meaning based on scope: databases, containers, then items.
- `cd ..` returns to the previous parent scope.
- Item listing respects the maximum item count.

### 5. Create And Delete Database And Container

Goal: Validate basic management operations on disposable resources.

Steps:

1. Connect to a test account.
2. Run `mkdb BugBashDb`.
3. Run `cd BugBashDb`.
4. Run `mkcon BugBashContainer /pk`.
5. Run `ls`.
6. Run `rmcon BugBashContainer`.
7. Run `cd ..`.
8. Run `rmdb BugBashDb`.

Expected:

- Create commands either succeed or return clear validation errors.
- Newly created resources appear in `ls`.
- Cleanup commands do not affect unrelated resources.

### 6. Create, Query, Print, And Remove Items

Goal: Validate common item workflows.

Steps:

1. Navigate to a test container.
2. Pipe a JSON item into `mkitem`, for example `{ "id": "bugbash-1", "pk": "bugbash", "name": "first" }`.
3. Run `query "SELECT * FROM c WHERE c.pk = 'bugbash'"`.
4. Run `print bugbash-1 bugbash`.
5. Remove the item with `rm bugbash` or the documented pattern for the current container.
6. Query again.

Expected:

- Valid JSON creates an item.
- Query returns the inserted item.
- `print` returns the expected item by id and partition key.
- Removed items no longer appear.

### 7. Query Limits And Large Result Sets

Goal: Confirm listing and query limits behave predictably.

Steps:

1. Use a container with more than 100 items, or insert enough test items.
2. Run `ls`.
3. Run `ls -m 10`.
4. Run `ls -m 0`.
5. Run `query -m 10 "SELECT * FROM c"`.
6. Run `query "SELECT * FROM c"`.

Expected:

- `ls` default limit is clear when it limits results.
- `-m 10` returns at most 10 items.
- `-m 0` disables the explicit limit for commands that support it.
- Query output remains readable and does not hang unexpectedly.

### 8. Output Formats And Filtering

Goal: Validate data can be scanned and filtered.

Steps:

1. Run `ls` at each scope.
2. Run `ls <filter>` with a partial database, container, or item name.
3. Run `ls -f json` or another documented format if available.
4. Run `ls -k <property> <filter>` inside a container.

Expected:

- Filters match expected resources.
- Format options produce parseable output or a clear unsupported-format error.
- Key-based filtering works on item properties.

### 9. Script Execution

Goal: Validate users can automate repeatable work.

Steps:

1. Create a small `.csh` script that runs `pwd`, `ls`, and one query.
2. Run it with `cosmosdbshell -c "<script-path>" --connect "<connection-string>"`.
3. Run it from inside the interactive shell.
4. Add a script argument and read it as `$1`.

Expected:

- Scripts run in the expected scope.
- `-c` exits after execution.
- Arguments are available to the script.
- Errors include script location or enough context to debug.

### 10. Piping And Redirection

Goal: Validate shell integration for command-line users.

Steps:

1. Pipe a command into the shell, for example `echo "pwd" | cosmosdbshell --connect "<connection-string>"`.
2. Redirect command output to a file, for example `echo "hello" > bugbash-output.txt` inside the shell.
3. Pipe JSON into `mkitem`.
4. Use the output file in another command where applicable.

Expected:

- Piped commands execute once and exit when expected.
- Redirection creates files with expected content.
- Invalid pipe input produces a clear error.

### 11. Error Handling And Recovery

Goal: Confirm failures are understandable and non-destructive.

Steps:

1. Connect with an invalid connection string.
2. Navigate to a missing database or container.
3. Run a malformed query.
4. Pipe malformed JSON into `mkitem`.
5. Retry with valid input after each failure.

Expected:

- Errors are clear and do not expose secrets.
- The shell remains usable after failures.
- Retrying with valid input succeeds without restarting the shell.

### 12. MCP Mode

Goal: Validate MCP server startup and basic lifecycle.

Steps:

1. Run `cosmosdbshell --mcp`.
2. Run `cosmosdbshell --mcp 6129`.
3. Confirm the process starts on the expected port.
4. Stop the process cleanly.
5. Try a port already in use.

Expected:

- Default port is `6128`.
- Custom port is honored.
- Port conflicts produce a clear error.
- The process shuts down cleanly.

## Cross-Platform Checks

Run a smaller smoke pass on each available platform:

- Windows PowerShell
- Windows Terminal or cmd
- macOS terminal
- Linux shell

Check quoting behavior, path separators, colors, keyboard input, and file redirection.

## Bug Report Template

```text
Title:
Scenario:
Environment:
Command or steps:
Expected result:
Actual result:
Impact:
Logs or screenshots:
Workaround:
```
