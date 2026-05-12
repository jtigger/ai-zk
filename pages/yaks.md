- Matt Wynne's
- https://github.com/mattwynne/yaks/
- Deliberately an extremely simple DAG of TODOs
	- in contrast with [[beads]] which is an elaborate issue tracker
-
- ## Key Features
	- ### Implements its own merge algorithm: State-based Conflict-free Replicated Data Type ([CRDT](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type))
		- changes are **idempotent**
		  logseq.order-list-type:: number
			- every change (i.e. event) gets a UUID (i.e. `event_id`)
			- when a merge occurs, events are deduped by that UUID, making a given change occur exactly once even after the merge
		- changes occur in **deterministic ordering**
		  logseq.order-list-type:: number
			- during a merge, events are sorted by `(timestamp, event_id)`
			- so long as two peers have synchronized clocks, they will agree on the order of events.
		-
		- uses `git` as a "wire format" and "storage backend"
		- writes git blobs directly (via a low-level API) and leverages `git`'s built-in patch apply to get the resulting current state.
		- #### The Merge Algorithm
			- git fetch origin `+refs/notes/yaks:refs/notes/yaks-peer` — grab the remote's event log into a temporary ref
			  logseq.order-list-type:: number
			- Set-union the two event lists (dedup by `event_id`)
			  logseq.order-list-type:: number
			- Sort the union by `(timestamp, event_id)`
			  logseq.order-list-type:: number
			- Rebuild the local ref by replaying all events in order
			  logseq.order-list-type:: number
			- Force-push the result back: `git push origin +refs/notes/yaks`
			  logseq.order-list-type:: number
			- Delete the temporary peer ref
			  logseq.order-list-type:: number
-