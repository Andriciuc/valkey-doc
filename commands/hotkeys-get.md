The `HOTKEYS GET` command returns the hottest keys detected during the most
recently completed detection window, ordered by estimated queries per second
(QPS), highest first. It takes no arguments.

See [`HOTKEYS`](hotkeys.md) for how detection is enabled and configured, what
an empty result can mean, and how state is reset.

A key that receives accesses but was never written is tracked the same as
any other key. This surfaces load against a key that does not exist, for
example from a bad key template or a stampede against a key an eviction just
removed.

## Examples

Enable detection and read the hottest keys:

```bash
127.0.0.1:6379> CONFIG SET hotkeys-top-k 16
OK
127.0.0.1:6379> HOTKEYS GET
1) 1) "key"
   2) "product:8fd21a"
   3) "db"
   4) (integer) 0
   5) "qps"
   6) (integer) 48200
```

Before the first window has completed, or while detection is disabled:

```bash
127.0.0.1:6379> HOTKEYS GET
(empty array)
```
