# Scripting Guide

This guide covers how to write scripts and create custom commands in Cosmos Shell.

## Overview

Cosmos Shell supports a scripting language that allows you to:

- Define reusable custom commands (functions)
- Use variables and expressions
- Implement control flow (if/else, loops)
- Compose pipelines with JSON data

---

## Lexical Structure

### Whitespace and Comments

- Spaces and tabs separate tokens
- Newlines end statements (you can also use `;`)
- `#` starts a comment that runs to end of line

```bash
# This is a comment
ls  # This is also a comment
cd mydb; ls  # Multiple statements on one line
```

### Identifiers

Command names, option names, and simple JSON keys can contain:
- Letters, digits, `_`, `-`, `.`, `\`, and `$`
- First character can be a letter, `_`, `-`, `.`, `\`, or `$`

Keywords (`if`, `while`, `for`, `do`, `loop`, `def`, `return`, `break`, `continue`) are case-insensitive.

---

## Variables

### Declaration and Assignment

```bash
$name = "value"
$count = 42
$active = true
$data = {"id": "1", "name": "test"}
$items = [1, 2, 3]
```

### Usage

Use variables by name or interpolate in strings:

```bash
echo $name
echo $"Hello, $name!"
cd $dbName
```

### Script Parameters

- `$0` - Script path
- `$1`, `$2`, ... - Positional arguments passed to the script

---

## Strings

### Single-Quoted Strings

Literal strings with no escape processing:

```bash
echo 'Hello, World!'
echo 'It''s a test'  # Escape single quote by doubling
```

### Double-Quoted Strings

Support backslash escapes:

```bash
echo "Line 1\nLine 2"   # \n = newline
echo "Tab:\tValue"       # \t = tab
echo "Quote: \""         # \" = quote
echo "Backslash: \\"     # \\ = backslash
```

### Interpolated Strings

Variable substitution with `$"..."`:

```bash
$name = "Ada"
echo $"Hello, $name!"    # Output: Hello, Ada!
echo $"Count: $count"    # Variable interpolation
echo $"Braces: {{}}"     # Literal braces with {{ and }}
```

---

## Numbers

```bash
$x = 42        # Integer
$y = 314       # Integer
$z = -1        # Negative (unary minus)
```

---

## Operators

### Arithmetic

| Operator | Description |
|----------|-------------|
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division |
| `%` | Modulo |
| `**` | Exponentiation |

```bash
$result = ($a + $b) * 2
$power = 2 ** 10
```

### Comparison

| Operator | Description |
|----------|-------------|
| `<` | Less than |
| `>` | Greater than |
| `<=` | Less than or equal |
| `>=` | Greater than or equal |
| `==` | Equal |
| `!=` | Not equal |

### Logical

| Operator | Description |
|----------|-------------|
| `&&` | Logical AND |
| `\|\|` | Logical OR |
| `^` | Logical XOR |
| `!` | Logical NOT |

### Grouping

Use parentheses for grouping: `( ... )`

```bash
if ($a > 0) && ($b < 10) { echo "in range" }
```

---

## Control Flow

### If/Else

```bash
if $n > 0 {
    echo "positive"
} else {
    echo "non-positive"
}

# Single line
if $active { echo "active" } else { echo "inactive" }
```

### While Loop

```bash
$i = 0
while $i < 3 {
    echo $i
    $i = $i + 1
}
```

### For Loop

Iterate over JSON arrays:

```bash
for $x in ["a", "b", "c"] {
    echo $x
}

# Using piped JSON
ls | for $db in $. {
    echo $"Database: $db"
}
```

### Do/While Loop

```bash
do {
    echo "tick"
    $count = $count + 1
} while $count < 5
```

### Loop (Infinite)

```bash
loop {
    # Use break to exit
    if $done { break }
    # Use continue to skip to next iteration
    if $skip { continue }
}
```

### Break and Continue

- `break` - Exit the current loop
- `continue` - Skip to the next iteration

---

## Custom Commands (def)

Define reusable commands (functions) that work like built-in commands.

### Syntax

```bash
def name [param1 param2 ...] {
    <statements>
}
```

### Basic Example

```bash
# Define a greeting command
def greet [who] {
    echo $"Hello, $who!"
}

# Call it
greet "Cosmos"   # Output: Hello, Cosmos!
```

### Parameters

Arguments are positional and accessed by declared names:

```bash
def add [a b] {
    return ($a + $b)
}

add 2 3   # Returns 5
```

### Returning Values

Use `return <expression>` to return a value:

```bash
def add [a b] {
    return ($a + $b)
}

add 2 3 | echo $"sum=$."   # Output: sum=5
```

Returned JSON can be used in pipelines:

```bash
def range3 {
    return [1, 2, 3]
}

range3 | for $n in $. {
    echo $"n=$n"
}
```

### Scope

- Variables assigned inside a function don't leak to the caller
- Global variables remain readable inside functions

---

## JSON and JSON Paths

### JSON Literals

```bash
# Object
$obj = { id: "1", pk: "A" }

# Array
$arr = [1, 2, 3]

# Nested
$data = {
    user: {
        name: "Ada",
        roles: ["admin", "user"]
    }
}
```

### JSON Path Access

Access values from piped JSON using paths starting with `$`:

```bash
# Access property
query "SELECT * FROM c" | echo $.Documents[0]

# Access nested property
echo '{"user":{"name":"Ada"}}' | echo $.user.name

# Access array element
ls | echo $.[0]

# Chain access
ls -m 1 | echo $.items[0].id
```

---

## Pipes and Pipelines

### Basic Piping

The `|` operator passes JSON results between commands:

```bash
# Pipe ls result to cd
ls | cd $.[0]

# Chain multiple commands
query "SELECT * FROM c" | echo $.Documents | for $doc in $. { echo $doc.id }
```

### JSON Flow

- Most commands return JSON
- The next command receives that JSON as input
- Use JSON paths to extract values

### Examples

```bash
# Enter first database
ls | cd $.[0]

# Enter first container in current database
ls | cd $.[0]

# Create items from array
echo '[{"id":"a"},{"id":"b"}]' | mkitem

# Process query results
query "SELECT * FROM c" | for $item in $.Documents {
    echo $"ID: $item.id"
}
```

---

## Practical Examples

### Setup Script

Create a database, container, and seed data:

```bash
def ensure_container [db container pk] {
    mkdb $db
    cd $db
    mkcon $container $pk
    cd $container
}

def seed [count] {
    # Assumes we are in a container context
    for $i in [1, 2, 3, 4, 5] {
        echo $"[{\"id\":\"item$i\",\"pk\":\"$i\",\"value\":$i}]" | mkitem
    }
}

# Usage
connect $1
ensure_container sampledb items /pk
seed 5
```

### Batch Operations

```bash
def delete_all_items {
    query "SELECT c.id, c.pk FROM c" | for $item in $.Documents {
        rm $item.id
    }
}
```

### Data Migration

```bash
def copy_items [sourceContainer targetContainer] {
    cd $sourceContainer
    $items = query "SELECT * FROM c"
    cd ..
    cd $targetContainer
    echo $items.Documents | mkitem
}
```

### Conditional Logic

```bash
def process_item [id pk] {
    $item = print $id $pk
    if $item.status == "pending" {
        echo $"Processing $id..."
        # Process logic here
    } else {
        echo $"Skipping $id (status: $item.status)"
    }
}
```

---

## Running Scripts

Save scripts with a `.run` extension and execute them:

```bash
# Run a script
./myscript.run

# Run with arguments
./setup.run "https://myaccount.documents.azure.com:443/"
```

Access arguments in the script:
- `$0` - Script path
- `$1` - First argument
- `$2` - Second argument, etc.

---

## Tips and Best Practices

1. **Use `def` for repeatable sequences** - Encapsulate connect, navigate, create, and seed operations

2. **Return JSON from functions** when results will be consumed by other commands

3. **Use interpolated strings** (`$"..."`) when composing JSON for `mkitem`

4. **Prefer pipelines** over temporary variables when possible

5. **Use comments** to document complex scripts

6. **Test incrementally** - Run commands interactively before adding to scripts

---

## Next Steps

- [Command Reference](COMMANDS.md) - Full list of available commands
- [MCP Server Guide](MCP-SERVER.md) - Enable AI assistant integration
