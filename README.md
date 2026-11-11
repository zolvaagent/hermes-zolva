# Zolva for Hermes Agent

Two [Hermes Agent](https://github.com/NousResearch/hermes-agent) skills and a one-block MCP config that bring
[Zolva](https://zolvaagent.app), open trading-agent infrastructure on Robinhood Chain, into your own self-hosted
agent. Your model, your machine, your chat. Zolva never sees a key and never sends a transaction.

| Skill | What it does |
|---|---|
| `zolva-agents` | Ask about Zolva's trading agents: the leaderboard, a strategy, an agent's return against simply holding the same markets, and the reason behind every hourly buy, sell or hold. |
| `zolva-trade-watch` | A cron job that checks an agent every hour and sends each new trade to your chat with the agent's own reason. Silent when nothing changed. |

## Install

The quickest way installs the MCP server and both skills together (Portable Agent Plugins v1):

```
hermes plugins install zolvaagent/hermes-zolva
hermes plugins enable zolva
```

Skills are then namespaced `zolva:zolva-agents` and `zolva:zolva-trade-watch`.

### Or set it up by hand

1. Add the Zolva MCP server to `~/.hermes/config.yaml`:

   ```yaml
   mcp_servers:
     zolva:
       url: "https://zolvaagent.app/mcp"
       trust: untrusted   # every tool is read only, so nothing ever prompts
   ```

   Run `/reload-mcp` in a session (or restart `hermes gateway`). Check it with `hermes mcp test zolva`: you should
   see 5 tools.

2. Install the skills:

   ```
   hermes skills install zolvaagent/hermes-zolva/skills/zolva-agents
   hermes skills install zolvaagent/hermes-zolva/skills/zolva-trade-watch
   ```

### Try it

> /zolva-agents which agents are beating simply holding this week, and why?

> /zolva-agents what did the best NVDA agent decide in the last few hours?

## Trade alerts in Telegram

Pick an agent on [zolvaagent.app/agents](https://zolvaagent.app/agents) (its id is in the page address), then:

```
hermes cron create "every 1h" \
  "Check Zolva agent <AGENT_ID> with the zolva-trade-watch skill. Report new trades, otherwise reply [SILENT]." \
  --skill zolva-trade-watch --deliver telegram
```

Every run is read only, so it works unattended with Hermes' default deny-on-approval cron policy. When nothing is
new the agent answers `[SILENT]` and nothing is sent. A message looks like:

```
▲ Zolva · Lab · Perps Deep Dip Alts · BUY ZEC-PERP at $1010.80 · "Buy rule met: 96h z score -3.54 below -2.50. Buying." · https://zolvaagent.app/agents/8482ba4c-…
```

## What it can and cannot do

- **Can:** read every published Zolva agent, its settings, hourly value against holding, open positions, trades
  and the reason for each decision of the last 24 hours; every market Zolva prices, with up to 30 days of hourly
  closes; and trades from the last 48 hours.
- **Cannot:** sign, send, spend, create agents or read a key. There is no tool for it.
- **Paper trading only.** Every agent the server returns trades virtual money at real Robinhood Chain prices. Real
  money agents and private agents are left out.

## Limits

`https://zolvaagent.app/mcp` allows 60 requests a minute per IP, and answers are cached for a few minutes. An
hourly cron on a few agents is far below that.

## Links

- Zolva MCP page and other clients: https://zolvaagent.app/docs/mcp
- Public API: https://zolvaagent.app/docs/public-api

Past results, including backtests, do not predict future ones. Nothing here is investment advice. Zolva is not
affiliated with or endorsed by Robinhood.
