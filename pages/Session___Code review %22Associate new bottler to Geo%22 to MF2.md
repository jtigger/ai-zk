-
- # Key Prompts
	- ```
	  We're going to perform a code review.
	  The focus of this one is to ensure that:
	  1. what was described is fully implemented
	  2. what was implemented is no more than what was described.
	  
	  The scope of the work is described in Shortcut story sc-68333.
	  - Note that that story refers to some docs that provide background context as well.
	  
	  The corresponding PR is https://github.com/mechanical-orchard/mf2/pull/600
	  - Use the `gh` CLI to fetch the conversations around this PR
	  
	  Also:
	  - if there's any ambiguity in the Shortcut story, there _is_ some chatter in Slack about this work
	    - There's this thread in the #proj-kdp-vintro channel: https://mechanical-orchard.slack.com/archives/C06LH7AQF4L/p1776205071665389
	  
	  Use your Superpowers as a guide for doing the code review and how to do all this with discipline.
	  
	  Let's start by establishing an approach to this activity.
	  
	  ```
	-
- # Notes
	- Created [[Use Case -- Performing an in-depth Code Review]]
	- Used [[General Config/Superpowers]] specifically for driving the Code Review part.
	- Used [[General Config/Shortcut]] via #MCP as primary source of context
	-
	- Found a meaningful conversation happening in Slack (about a bug mentioned in the Shortcut story; but the resolution conversation happening in Slack)
	- Decided to integrate slack:
		- [[Session/Best integration method for Slack]]
		- Updated [[General Config/Slack]] using #MCP
		-
	- And while we're at it ensure that GitHub is available
		- [[General Config/GitHub]]
			- Claude Code's system prompt explicitly directs: "Use the gh command via the Bash tool for ALL GitHub-related tasks."
		- Ensured that the `gh` auth was pointing to my `mechanical-orchard` org
		- Updated my prompt to reinforce using `gh` for GitHub PR context.
		-
-
- # Context Resources
	- https://app.shortcut.com/mo-vintro/story/68333/mike-can-assign-a-budget-holder-to-an-organizational-unit
	- https://github.com/mechanical-orchard/mf2/pull/600#discussion_r3096117790