The `HOTKEYS RESET` command clears all hot key detection state on this node,
both the live, still-accumulating window and the last completed one. Use it
to start monitoring from a clean baseline, for example right after changing
`hotkeys-top-k`, `hotkeys-sampling-percentage`, or `hotkeys-window-seconds`.

Detection state is also cleared automatically by anything that discards an
entire database or slot range: [`FLUSHDB`](flushdb.md), [`FLUSHALL`](flushall.md), a full sync or RDB
reload that empties the dataset, a cluster reset, and dropping a slot.
`HOTKEYS RESET` is for clearing it on demand outside of those events.
Ordinary key access and removal is treated as activity rather than a reset,
so [`DEL`](del.md) and [`UNLINK`](unlink.md) count as accesses, and expiry and eviction do not clear
state.

## Examples

```bash
127.0.0.1:6379> HOTKEYS RESET
OK
```
