# Core dumps

## What is core dump?

When your process crashes, it produces a core dump. It's a snapshot of the state of your app (memory, call stack) on it's death bed.

If you didn't encounter a core dump before, it's probably because your operating system is configured to limit dump file sizes to 0 bytes by default.

### Enabling core dumps
```bash
ulimit -c unlimited
```
Change the limit of core dump size in your linux OS.

This can also be configured with any Ops tools for infrastructure automation. Setting it to unlimited in production is not a great idea, especially if auto-restart is enabled.

If you're running in containers, make sure they're configured not to discard the storage location where dumps are written.

## Core dump analysis

[![asciicast](https://asciinema.org/a/101812.png)](https://asciinema.org/a/101812)

- install lldb in your system (from apt)
- install llnode according to readme [llnode notes](llnode.md)
- enable writing core dumps `ulimit -c unlimited`
- run the app and see it break
  - node dumps when it runs out of memory or segfaults
  - set `--abort-on-uncaught-exception` to dump core on crash
  - call `process.abort()`
  - or just use `gcore <PID>` to generate core dump on demand
- get the `core` file
- if you are using lldb version 3.8 (not needed if you are using lldb version 3.9 or later):
  - segment it with a script from llnode `./llnode/scripts/readelf2segments.py ./core > core.ranges`
  - let lldb know where to look `export LLNODE_RANGESFILE=core.ranges`
- run `lldb -c core`
- use `v8` commands to analyze

## How to find stuff in a core dump

[TBD](tbd.md)

## When should I use core dumps?

[TBD](tbd.md)
