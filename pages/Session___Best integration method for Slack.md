- #[[Claude Code]]
- When I attempted to `/export` (first to clipboard, then to file) it failed
-
- After installing, `/plugins` complained that this plugin needed to authenticate.
	- I did this through the browser (OAuth?) based flow.
- # Session Transcript
	- ```
	  claude
	  ╭─── Claude Code v2.1.114 ───────────────────────────────────────────────────────────────────────────────────────────────╮
	  │                                                    │ Tips for getting started                                          │
	  │           Welcome back Johnny Cyberseed!           │ Run /init to create a CLAUDE.md file with instructions for Claude │
	  │                                                    │ ───────────────────────────────────────────────────────────────── │
	  │                       ▐▛███▜▌                      │ Recent activity                                                   │
	  │                      ▝▜█████▛▘                     │ No recent activity                                                │
	  │                        ▘▘ ▝▝                       │                                                                   │
	  │    Opus 4.7 (1M context) · API Usage Billing ·     │                                                                   │
	  │    Mechanical Orchard                              │                                                                   │
	  │                ~/workspace/kdp/mf2                 │                                                                   │
	  ╰────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
	  
	  ❯ What's the most efficient and effective way to provision Claude Code access to Slack?
	  
	    ⎿  Response:
	         Based on my research, here's the most efficient and effective way to provision Claude Code access to Slack:
	  
	         Summary: Recommended Approach
	  
	         Best option: Use the official Slack MCP plugin – lowest friction, best capability coverage, and officially maintained by Slack.
	  
	         Key Findings
	  
	         1. Official Options
	         - Slack published an official MCP server that works with Claude Code
	         - The Slack app on claude.ai is a separate product (web-based chat); Claude Code accesses Slack via MCP
	         - Anthropic archived its standalone Slack MCP server in May 2025, but Slack maintains the official implementation
	  
	         2. Community Alternatives
	         - @modelcontextprotocol/server-slack (npm) – the reference implementation from the MCP registry, still maintained
	         - korotovsky/slack-mcp-server – community fork with extended features (no permissions required, GovSlack support)
	         - Both are functional alternatives but not recommended over the official Slack server for standard use
	  
	         3. Auth & Scopes
	         - Bot token (xoxb-) is the recommended approach
	         - Required scopes: chat:write, channels:read, channels:history, groups:history
	         - Bot tokens cannot search (conversations_search_messages unavailable)
	         - User tokens (xoxp-) work but require more granular permissions; OAuth app installation requires workspace admin approval
	  
	         4. Setup for Claude Code
	         claude mcp add --transport http slack-composio "YOUR_MCP_URL" --headers "X-API-Key:YOUR_API_KEY"
	         Or use the Slack plugin directly via slash command within Claude Code:
	         /plugin install slack
	         Alternatively, edit .claude/settings.json and restart your session.
	  
	         5. Tradeoff & Winner
	         Use Slack's official MCP plugin (/plugin install slack or claude plugin install slack). It wins because:
	         - Workspace admin controls MCP approvals (transparent security)
	         - Automatic OAuth setup (no manual token management)
	         - Officially maintained by Slack (security updates guaranteed)
	         - Zero manual configuration complexity
	  
	         Next Steps:
	         1. Ask your Slack workspace admin to approve MCP integrations if not already enabled
	         2. Run /plugin install slack in Claude Code
	         3. Authenticate via OAuth when prompted
	         4. Start interacting with Slack channels, messages, and files directly in Claude
	  
	         Sources:
	         - Connect to Claude | Slack Developer Docs
	         - Claude Code in Slack - Claude Code Docs
	         - Slack MCP Server | Slack Developer Docs
	         - @modelcontextprotocol/server-slack - npm
	         - Slack Securely Powers Your Third-Party Agents | Slack Blog
	    ⎿  Done (8 tool uses · 14.7k tokens · 30s)
	    
	    ⏺ Recommended: Slack's official MCP plugin
	  
	    The clear winner — Slack officially maintains an MCP server, and Claude Code has first-class plugin support.
	  
	    Setup
	  
	    1. Ask your Slack workspace admin to approve MCP integrations (if not already enabled)
	    2. In Claude Code, run /plugin install slack
	    3. Authenticate via OAuth when prompted
	  
	    Why this over alternatives
	  
	    - Official & maintained: Slack owns it (Anthropic archived its own Slack MCP in May 2025)
	    - OAuth flow, no token juggling: avoids manually creating a Slack app, picking scopes, and pasting xoxb- tokens into settings
	    - Admin-governed: workspace admin controls approval, so it's transparent to your security team
	  
	    Alternatives (only if the plugin path isn't available to you)
	  
	    - @modelcontextprotocol/server-slack (npm) — community reference impl, requires manual Slack app + bot token with chat:write, channels:read, channels:history, groups:history
	    - korotovsky/slack-mcp-server — fork, useful if you need search (bot tokens can't call conversations_search_messages) or GovSlack
	  
	    Note: The Slack app at claude.ai/slack is a different product (chat Claude inside Slack) — not relevant for giving Claude Code access to Slack.
	    
	  ```