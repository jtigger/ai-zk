- a Mandatory Access Control policy module providing process sandboxing (disk and network)
- ## What it does
	- Seatbelt confines a process to a restricted set of operations
		- file reads/writes
		- network access
		- IPC
		- executing other binaries
		- sending signals
		- ...
	- based on a policy declared in a profile
	- Even if the process is compromised or buggy, it can't escape the box
- ## How it works
	- Based on The TrustedBSD Mandatory Access Control (MAC) layer
	  logseq.order-list-type:: number
	  collapsed:: true
		- At the kernel level, macOS includes a port of FreeBSD's TrustedBSD Mandatory Access Control (MAC) framework.
			- This framework adds hooks at virtually every security-sensitive syscall — open(), connect(), mmap(), fork(), etc. Each hook calls into registered policy modules to ask "is this allowed?"
		- Seatbelt is one such policy module: a kernel extension named Sandbox.kext (now built into the kernel on modern macOS).
	- Sandbox profiles written in Sandbox Profile Language (**SBPL**)
	  logseq.order-list-type:: number
	  collapsed:: true
		- Policies are written in Sandbox Profile Language (SBPL), which is a Scheme/TinyScheme dialect.
		- Example:
			- ```
			    (version 1)
			    (deny default)
			    (allow process-fork)
			    (allow file-read*
			        (subpath "/usr/lib")
			        (subpath "/System/Library"))
			    (allow network-outbound (remote tcp "*:443"))
			  ```
		- A profile is compiled into a binary policy blob and handed to the kernel via sandbox_init() / sandbox_init_with_parameters() (userspace) or set via __mac_execve / entitlements at exec time.
	- Enforced at the OS syscall level ()
	  logseq.order-list-type:: number
	  collapsed:: true
		- Once a process is sandboxed:
			- Every gated syscall traps into the kernel
			- The MAC framework consults the loaded Seatbelt policy
			- The policy evaluates the SBPL rules against the operation's parameters (path, port, signal, etc.)
			- The syscall either proceeds or returns EPERM
			- Sandbox decisions can also produce violation reports in Console.app / log show --predicate 'sender == "Sandbox"'.
	- App Sandbox (the developer-facing form)
	  logseq.order-list-type:: number
	  collapsed:: true
		- For Mac App Store apps, Apple ships prebuilt profiles keyed off entitlements in the app's code-signed Info.plist.
		- When you declare com.apple.security.network.client = true, the launchd/exec path applies a sandbox profile that allows outbound network.
		- Developers don't write SBPL directly — they pick entitlements, and the system maps those to a bundled profile.
	- System-wide use
	  logseq.order-list-type:: number
		- Seatbelt isn't just for App Store apps. macOS itself sandboxes many built-in services. Look in:
			- `/System/Library/Sandbox/Profiles/` — system profiles (*.sb files)
			- `/usr/share/sandbox/` — older location
			- Many `launchd` `plists` reference sandbox profiles directly
			- Examples: `mDNSResponder`, `cfprefsd`, Safari's WebContent process, `quicklookd`, etc., all run under Seatbelt profiles.
			- CLI: `sandbox-exec`
			- You can run an arbitrary command under a custom profile:
				- ```
				  sandbox-exec -f my-profile.sb /bin/bash
				  ```
			- This is officially deprecated (Apple stopped documenting SBPL), but it still works and is widely used by security researchers and tools like Chrome's renderer sandbox, Firefox's content process, and Claude Code's sandbox mode (which uses Seatbelt on
			  macOS to confine tool execution).
- ## Notable points
	- originally introduced in Mac OS X 10.5 Leopard (2007) under the name `sandbox_init`.
	- Internally, it's also called App Sandbox when exposed to third-party developers (since 10.7 Lion).
	- The underlying mechanism is sometimes referred to as TrustedBSD MAC Framework-based sandboxing.
	- SBPL is undocumented. Apple publishes the entitlement list but not the profile language. Reverse-engineered references exist (Dionysus Blazakis's "The Apple Sandbox", Jonathan Levin's MacOS Internals).
	- Default-deny is the right pattern — (`deny default`) then explicitly allow.
	- Profiles can be parameterized at load time, so one profile template can be reused with different paths/PIDs.
	- It's not a replacement for permissions — it layers on top of standard Unix DAC and macOS's TCC (the privacy framework behind "X wants to access your Camera" prompts).
	- So, in short: Seatbelt is a kernel-enforced, profile-driven, default-deny sandbox sitting on the TrustedBSD MAC framework, used pervasively by macOS itself and by any app that opts into App Sandbox.
- ## Further Reading
  collapsed:: true
	- #needs-formatting
	- Authoritative sources
	  
	    Honest caveat upfront: Apple has never officially documented SBPL (the Sandbox Profile Language). The deepest sources are reverse-engineered or come from the FreeBSD lineage. So "authoritative" splits into two tiers: primary (Apple/FreeBSD source) and
	     canonical secondary (researchers whose work the community has accepted as the reference).
	  
	    Primary sources
	  
	    1. Apple's open-source releases
	  
	    Apple publishes parts of macOS on opensource.apple.com. The relevant components:
		- xnu — the kernel, including the MAC framework integration (security/mac_framework.h, security/mac_policy.h, the mac_* hook implementations throughout the VFS, Mach, BSD layers).
		- Libsystem / libsandbox — the userspace sandbox_init() / sandbox_init_with_parameters() API surface (header: sandbox.h).
		- Older releases included sandbox-related sources; modern releases have stripped most of Sandbox.kext (it's now in the kernelcache and largely closed).
		  
		  The xnu source is the single most authoritative artifact for how MAC hooks are wired in.
		  
		  2. Apple's developer documentation
		  
		  What Apple does document officially:
		- App Sandbox Design Guide (developer.apple.com archive) — the developer-facing form.
		- Entitlements reference — the keys that map to bundled sandbox profiles.
		- sandbox-exec(1) / sandbox_init(3) man pages — terse, but official. Note sandbox-exec is marked deprecated.
		- /System/Library/Sandbox/Profiles/*.sb on a running Mac — these are the actual policies the system enforces, written in SBPL. Reading them is as authoritative as it gets for what the syntax looks like in practice.
		  
		  3. FreeBSD TrustedBSD
		  
		  The MAC framework originated in FreeBSD. The TrustedBSD project (trustedbsd.org, now largely folded into FreeBSD proper) and the FreeBSD handbook chapter on MAC are the canonical reference for the framework itself. Apple's MAC hooks are a port — the
		  architecture, hook model, and design rationale are documented there.
		- Robert Watson's papers on TrustedBSD (USENIX/BSDCan) — Watson is the original author. His "The TrustedBSD MAC Framework" paper is the foundational write-up.
		  
		  Canonical secondary sources
		  
		  4. Dionysus Blazakis — "The Apple Sandbox" (Black Hat DC 2011)
		  
		  The first serious public reverse-engineering of Seatbelt. Paper + slides. Still the most-cited starting point. Covers the kext, the SBPL compiler, and how profiles are loaded into the kernel.
		  
		  5. Jonathan Levin — *MacOS and OS Internals book series
		  
		  Three volumes; Volume III: Security & Insecurity has the most thorough treatment of Seatbelt, the AMFI/Sandbox interaction, entitlements, and the MAC layer that exists outside Apple. Levin's newosxbook.com site has supplementary material and updates
		  per macOS version. This is the reference most kernel/security folks point to today.
		  
		  6. Stefan Esser / Patrick Wardle / Csaba Fitzl
		  
		  Conference talks (Black Hat, Objective by the Sea, DEF CON) on sandbox escapes and internals. Useful for current behavior on recent macOS versions, where Levin's book may lag.
		- Csaba Fitzl's training/blog posts on macOS sandbox internals are particularly current.
		- Patrick Wardle's The Art of Mac Malware (No Starch) covers Seatbelt at the level needed to understand evasion.
		  
		  7. Chromium and Firefox sandbox source
		  
		  Both browsers use Seatbelt for renderer/content processes and have well-commented profile files in their open-source trees:
		- Chromium: sandbox/policy/mac/ and the *.sb profiles in the source tree.
		- Firefox: security/sandbox/mac/.
		  
		  These are valuable because they're production SBPL maintained by people who've talked to Apple security and have to keep up with macOS releases. Reading them is one of the best practical references for SBPL idioms.
		  
		  What I'd actually recommend, in order
		  
		  1. Read profiles in /System/Library/Sandbox/Profiles/ on your own Mac — concrete and current.
		  2. Levin, Volume III — the cohesive narrative.
		  3. Blazakis's 2011 paper — historical/architectural foundation.
		  4. Watson's TrustedBSD papers — for the MAC framework itself.
		  5. Chromium's sandbox source — for living, maintained SBPL examples.
		  6. Apple's xnu source — when you need ground truth on a specific MAC hook.
		  
		  I'm deliberately not pasting URLs for most of these because the canonical locations have moved over the years (opensource.apple.com restructured, trustedbsd.org redirected, conference archives migrated). Searching the title + author will land you on
		  the current home reliably.