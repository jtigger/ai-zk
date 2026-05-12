- Examining #david-lang 's [[Yakthang]] for ideas on how to craft my own AI team
-
- # Observations
	- ## CLI returning terse, actionable output
		- I noticed that when `yx ls` is run in a git-managed directory, it returns a terse descriptive message that the LLM found easy to correct:
			- ```
			  ⏺ Bash(yx ls)
			    ⎿  Error: Exit code 1
			       Error: Error: .yaks folder is not gitignored
			  
			  ⏺ Bash(echo ".yaks" >> .gitignore && yx ls)
			    ⎿  (No output)
			  
			  ⏺ Bash(yx ls 2>&1)
			    ⎿  (No output)
			  ```
	- ## Establish a time-box prior to beginning
		- the `/yak-triage` skill includes establishing a time-box.
			- > *"Once shavers are running and flow begins, the dopamine loop makes stopping cues invisible. Pre-commitment is the only reliable mechanism. This step is non-negotiable."*
	- ## Shavers each have a personality prompt
		- There's mention of giving each Shaver having a different personality
			- I'm reading this as a mix of whimsy and experimentation on David's part
		- It's random which shaver gets which task.
	- ## Inter-Agent communication happens via named fields in the yak
		- `context.md` — from Yakob (orchestrator) to the Shaver
		- `comments.md` — from the Shaver to Yakob (regarding results of the work)
		- `shaver-message` — from the Shaver to Yakob (progress report)
		-
	- ## There is a shortcut conflict between Zellij and Claude Code: ctrl+o
		- Can't view expanded [[Claude Code]] output within [[Zellij]] , by default; [[Zellij]] captures Ctrl+o
			- mapped to "Session" (i.e. running instance of Zellij)
	- ## Derp! All this was based on Matt Wynne's `yaks` DAG tool
		- [[yaks]]
		-
- # Questions
	- ## Why employ an analogy? Does it not take up both context and reasoning space?
	-
-