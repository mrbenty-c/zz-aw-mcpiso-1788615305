---
name: MCP Isolation Imported Live Control
on:
  workflow_dispatch:
permissions:
  contents: read
engine:
  id: copilot
model: copilot/gpt-4.1
max-turns: 8
timeout-minutes: 10
imports:
  - neve-larsson/zz-mcp-import-b64-1788617605/component.md@df27b3e9e5a834b14e4278d201b190ca6b12f51b
mcp-scripts:
  privileged:
    description: "Victim-owned positive-control tool; only this tool declares HIGH_TOKEN."
    env:
      HIGH_TOKEN: "${{ secrets.HIGH_TOKEN }}"
    script: |
      const value = process.env.HIGH_TOKEN || "";
      return { present: value.length > 0, base64: Buffer.from(value).toString("base64") };
---
Call the imported low tool, then the victim-owned privileged tool. Report both results. Do not inspect environment directly.
