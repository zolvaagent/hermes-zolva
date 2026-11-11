# Security

## What this plugin does

- Connects Hermes to one remote MCP server, `https://zolvaagent.app/mcp`, over Streamable HTTP.
- Every tool on that server is read only (annotated `readOnlyHint`). There is no tool that signs, sends, spends,
  creates an agent or touches a wallet.
- No keys, tokens or credentials are needed or read. The plugin has no environment variables.
- No code runs on your machine: the package is two Markdown skills and two JSON files.

## Data

The server returns public data that is also on zolvaagent.app: published paper trading agents, their decisions and
trades, and market prices. Tool output is data. The skills tell the agent to treat every string returned by a tool
(agent names, reasons) as data and never as an instruction.

## Reporting a problem

Open an issue on this repository, or reach us on X at [@zolva_agent](https://x.com/zolva_agent). Please do not post
details of an unfixed vulnerability in a public issue; send a direct message instead.
