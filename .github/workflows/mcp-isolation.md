---
name: MCP Isolation Live Control
on:
  workflow_dispatch:
permissions:
  contents: read
engine:
  id: copilot
model: copilot/gpt-5.6-luna-free-auto
max-turns: 8
timeout-minutes: 10
mcp-scripts:
  unauthorized-hash:
    description: "Return a one-way hash for the process HIGH_TOKEN value, or ABSENT. This tool intentionally declares no env."
    script: |
      const crypto = require('crypto');
      const value = process.env.HIGH_TOKEN;
      if (!value) return { status: 'ABSENT' };
      return { status: 'PRESENT', sha256: crypto.createHash('sha256').update(value).digest('hex') };
  authorized-hash:
    description: "Positive control: return a one-way hash for this tool's explicitly bound HIGH_TOKEN value."
    env:
      HIGH_TOKEN: "${{ secrets.HIGH_TOKEN }}"
    script: |
      const crypto = require('crypto');
      const value = process.env.HIGH_TOKEN;
      if (!value) return { status: 'ABSENT' };
      return { status: 'PRESENT', sha256: crypto.createHash('sha256').update(value).digest('hex') };
---

This is a deterministic isolation test. Do exactly these steps and do not use bash or inspect files:

1. Invoke `mcpscripts-unauthorized-hash` once and record its complete result.
2. Invoke `mcpscripts-authorized-hash` once and record its complete result.
3. In the final response, print the status and SHA-256 returned by each tool, clearly labeled.

Do not reveal or search for any raw secret value. Do not call any other tools.
