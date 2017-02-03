# Core dump analysis

[![asciicast](https://asciinema.org/a/101812.png)](https://asciinema.org/a/101812)

- install lldb in your system
- install llnode according to readme
- enable writing core dumps `ulimit -c unlimited`
- run the app and see it break!
  - node dumps when it runs out of memory
  - set `--abort-on-uncaught-exception`
  - call `process.abort()`
- get the `core` file
- segment it with a script from llnode `./llnode/scripts/readelf2segments.py ./core > core.ranges`
- let lldb know where to look `export LLNODE_RANGESFILE=core.ranges`
- run `lldb -c core`
- use `v8` commands to analyze

## How to find stuff

TBD
