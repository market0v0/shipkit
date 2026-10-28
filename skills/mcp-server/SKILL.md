---
name: mcp-server
description: Scaffolds and reviews Model Context Protocol (MCP) servers in Python (FastMCP) or TypeScript, with well-described tools, input validation, read/write separation and safe error handling. Use when building an MCP server or exposing an API or database to Claude.
---

# Building an MCP server

An MCP server is an API whose user is a model. Design every tool so a model can pick it correctly from its name and description alone.

## Tool design

- **One job per tool**, named as an action: `search_invoices`, `get_shipment`, `approve_refund`.
- **Descriptions say when to use it**, what it returns, and its limits: "Search invoices by customer or status. Returns at most 50, newest first."
- **Typed, validated inputs** with sensible defaults and bounds (page size, date ranges). Reject bad input with a message the model can act on.
- **Separate reads from writes.** Writes are their own tools, require explicit IDs, are idempotent, and return what changed.
- **Return compact, structured results** (IDs, key fields, totals), not whole documents. Offer a `get_*` tool for detail.
- **Errors explain the fix:** "No customer 'acme'. Did you mean 'Acme Corp' (id 42)?" beats a stack trace.

## Python (FastMCP) skeleton

```python
from fastmcp import FastMCP
from pydantic import Field

mcp = FastMCP("ops-data")

@mcp.tool
def search_invoices(
    customer: str | None = None,
    status: str | None = Field(None, description="open, paid or void"),
    limit: int = Field(20, ge=1, le=50),
) -> list[dict]:
    """Search invoices by customer and/or status. Newest first, at most 50."""
    query = {k: v for k, v in {"customer": customer, "status": status}.items() if v}
    return [compact(doc) for doc in db.invoices.find(query).sort("issued_at", -1).limit(limit)]

if __name__ == "__main__":
    mcp.run()  # stdio by default; mcp.run(transport="http") for remote use
```

## Safety checklist

- Credentials come from environment variables, never tool arguments or code.
- Database access uses a least-privilege user; read-only servers get a read-only user.
- Cap result sizes and query costs; never pass model input into raw query strings.
- Log every write tool call with its arguments and result.

## Test it

Run the MCP Inspector (`npx @modelcontextprotocol/inspector`) against the server, call each tool with good and bad input, then add it to Claude Code with `claude mcp add` and try real requests.
