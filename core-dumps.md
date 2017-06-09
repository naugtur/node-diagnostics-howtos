# Core dumps

## What's a core dump?

When your process crashes really badly, it produces a core dump. It's a snapshot of the state of your app (memory, call stack) on it's death bed.

If you didn't encounter a core dump before, it's probably because the operating system is configured to limit dump file sizes to 0 bytes by default.

## Core dump analysis

[![asciicast](https://asciinema.org/a/101812.png)](https://asciinema.org/a/101812)

- install lldb in your system (from apt)
- install llnode according to readme [llnode notes](llnode.md)
- enable writing core dumps `ulimit -c unlimited`
- run the app and see it break!
  - node dumps when it runs out of memory
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
