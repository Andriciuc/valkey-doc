This is a container command for hot key detection commands. To see the list of
available commands you can call `HOTKEYS HELP`.

## What it does

The [`HOTKEYS GET`](hotkeys-get.md) command samples client key accesses and report the hottest keys.
Detection works by sampling accesses, accumulating counts in a
live window, and freezing that window on a fixed interval; each
`HOTKEYS GET` reports on the most recently frozen window, not on the live one
currently accumulating.

Detection is disabled by default and consumes no resources while off. You can enable it
by setting `hotkeys-top-k` to a value greater than `0`. State is per
node: each server tracks its own accesses independently.

## Configuration

| Parameter | Default | Range | Meaning |
| --- | --- | --- | --- |
| `hotkeys-top-k` | 0 (off) | 0-1000 | How many keys to track; doubles as the on/off switch |
| `hotkeys-sampling-percentage` | 1 | 1-100 | Percentage of client key accesses sampled |
| `hotkeys-window-seconds` | 1 | 1-300 | Length of the reporting window |

All three are runtime configurable with [`CONFIG SET`](config-set.md).

A window closes on the server's periodic task, so it can run slightly past
its nominal boundary. `INFO hotkeys` reports the measured duration as
`hotkeys_last_window_duration_ms`. If the server stalls long enough that the
open window grows past twice `hotkeys-window-seconds`, it is dropped rather
than reported, so a report is never presented as an average over a longer
span than configured; `HOTKEYS GET` returns an empty result until the next
window completes.

## State and resets

Hot-key state, both the live window and the last completed one, is cleared
by `HOTKEYS RESET`, and automatically by anything that discards an entire
database or slot range: [`FLUSHDB`](flushdb.md), [`FLUSHALL`](flushall.md), a full sync or RDB reload
that empties the dataset, a cluster reset, and dropping a slot. Ordinary key
access and removal counts as activity rather than a reset: [`DEL`](del.md) and
[`UNLINK`](unlink.md) count as accesses, and expiry and eviction do not clear state.

Renaming or moving a key does not carry its statistics to the new name or
database. The old `(key, db)` entry stops accruing hits immediately but can
still appear in a report until the window rotates.
