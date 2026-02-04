# Security Considerations

> ⚠️ **Important:** Read this document carefully before enabling MCP or using Cosmos Shell in production environments.

## Overview

Azure Cosmos DB Shell provides powerful capabilities for managing your databases. With power comes responsibility—this document outlines security considerations and best practices.

---

## MCP Server Security

### How MCP Works

The Model Context Protocol (MCP) server allows external clients (like AI assistants) to programmatically control Cosmos Shell. When enabled:

- An HTTP server runs locally (default port 6128)
- Connected MCP clients can execute any shell command
- Commands run with your local user permissions

### Risks

| Risk | Description |
|------|-------------|
| **Unauthorized Access** | Any process that can reach the MCP port can control the shell |
| **Data Exposure** | Command outputs may be transmitted to remote LLM services by your MCP client |
| **Resource Modification** | Connected clients can create, modify, and delete resources |
| **Credential Exposure** | Connection strings or data could be captured in command history or logs |

### Mitigation Strategies

#### 1. Keep MCP Bound to Localhost

The MCP server should only listen on `localhost` (127.0.0.1). Never expose it to external networks.

```json
{
    "cosmosDB.shell.MCP.port": 6128
}
```

#### 2. Restrict Firewall Access

Ensure your firewall blocks external access to the MCP port:

**Windows (PowerShell Admin):**
```powershell
# Block external access to MCP port
New-NetFirewallRule -DisplayName "Block MCP External" -Direction Inbound -LocalPort 6128 -Protocol TCP -Action Block -RemoteAddress Any
```

**macOS/Linux:**
```bash
# Verify only localhost is listening
netstat -an | grep 6128
# Should show 127.0.0.1:6128, not 0.0.0.0:6128
```

#### 3. Review MCP Client Requests

Most MCP clients show tool/command requests before execution. **Always review requests** before approving, especially:

- Delete operations (`rm`, `rmdb`, `rmcon`)
- Bulk modifications
- Commands with connection strings

#### 4. Don't Auto-Approve Destructive Operations

Configure your MCP client to require manual approval for operations that:
- Delete resources
- Modify data
- Access sensitive information

#### 5. Disable MCP When Not Needed

If you're not actively using AI assistance:

```json
{
    "cosmosDB.shell.MCP.enabled": false
}
```

Or avoid the `--mcp` flag when starting from command line.

---

## Authentication Best Practices

### Prefer Azure AD Over Connection Strings

| Method | Security Level | Recommendation |
|--------|---------------|----------------|
| Azure AD / Entra ID | ✅ High | **Recommended** |
| Managed Identity | ✅ High | For Azure-hosted scenarios |
| Connection String | ⚠️ Medium | Avoid when possible |
| Account Keys | ❌ Low | Avoid |

#### Using Azure AD

```bash
# Connect with Azure AD (recommended)
connect "https://myaccount.documents.azure.com:443/"
```

This opens a browser for authentication and doesn't store credentials.

#### If You Must Use Connection Strings

- Never commit connection strings to source control
- Use environment variables or secure vaults
- Rotate keys regularly
- Use read-only keys when write access isn't needed

### Apply Least-Privilege RBAC

Assign the minimum required permissions:

| Role | Use Case |
|------|----------|
| Cosmos DB Account Reader | Read-only exploration |
| Cosmos DB Data Contributor | Standard CRUD operations |
| Cosmos DB Data Owner | Full access including RBAC management |

---

## Data Protection

### Sensitive Data in Outputs

Be aware that command outputs may contain sensitive data:

- Document contents (PII, credentials, etc.)
- Query results
- Error messages with account details

If using MCP with a remote LLM:
- Treat all outputs as potentially transmitted to the LLM service
- Avoid querying sensitive data during AI-assisted sessions
- Review your MCP client's data handling policies

### Command History

Cosmos Shell maintains command history. To clear it:

```bash
CosmosShell --clearhistory
```

Avoid typing sensitive data (like connection strings) directly in commands.

### Logging

Check for logs that might contain sensitive information:
- VS Code Output panel logs
- Terminal history
- Any configured logging

---

## Network Security

### Local Development

For local development:
- Keep MCP bound to localhost
- Use Azure AD authentication
- Don't expose ports to the network

### Corporate Networks

On corporate networks:
- Follow your organization's security policies
- Use approved authentication methods
- Consider network segmentation

### Public Networks

On public networks (coffee shops, airports):
- **Do not enable MCP**
- Use VPN if accessing Cosmos DB
- Be cautious about shoulder surfing

---

## Checklist

Use this checklist before using Cosmos Shell:

### General
- [ ] Using Azure AD authentication (not connection strings)
- [ ] Applied least-privilege RBAC to your account
- [ ] Cleared command history of any sensitive data

### MCP Enabled
- [ ] MCP server bound to localhost only
- [ ] Firewall configured to block external access to MCP port
- [ ] MCP client configured to require approval for destructive operations
- [ ] Aware that outputs may be transmitted to remote LLM services
- [ ] Will disable MCP when not actively using AI assistance

### Production Data
- [ ] Not using production credentials in development
- [ ] Not querying PII/sensitive data during AI-assisted sessions
- [ ] Logs configured appropriately for compliance

---

## Incident Response

If you suspect unauthorized access:

1. **Immediately disable MCP** and disconnect from Cosmos DB
2. **Rotate credentials** - Regenerate connection strings/keys
3. **Review audit logs** in Azure Portal for suspicious activity
4. **Check command history** for unauthorized commands
5. **Report** to your security team following your organization's procedures

---

## Reporting Security Issues

If you discover a security vulnerability in Cosmos Shell:

1. **Do not** disclose it publicly
2. Contact the Cosmos DB team directly
3. Provide detailed reproduction steps
4. Allow time for a fix before any disclosure

---

## Additional Resources

- [Azure Cosmos DB Security Baseline](https://docs.microsoft.com/azure/cosmos-db/security-baseline)
- [Azure RBAC for Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-rbac)
- [Azure AD Authentication for Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-rbac#authenticate-with-azure-ad)

---

## Summary

| Do | Don't |
|----|-------|
| ✅ Use Azure AD authentication | ❌ Use connection strings in code |
| ✅ Keep MCP on localhost | ❌ Expose MCP to the network |
| ✅ Review MCP requests before approving | ❌ Auto-approve all operations |
| ✅ Disable MCP when not in use | ❌ Leave MCP running unnecessarily |
| ✅ Apply least-privilege RBAC | ❌ Use owner/admin keys for everything |
| ✅ Clear sensitive data from history | ❌ Type credentials in commands |
