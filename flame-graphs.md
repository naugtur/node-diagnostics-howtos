# Flame graphs

## What's flame graph useful for?

Flame graphs are a way of visualizing CPU time spent in functions. They will help you pin down where you spend too much time doing synchronous operations.

## How to flame graph

no more Solaris vms for flame graphs on linux!

- install lldb in your system (from apt)
- install llnode according to readme [llnode notes](llnode.md)
- install linux-tools-common (from apt)
- try running `perf` - it might complain about missing modules, install them too
- install stackvis `npm i -g stackvis`
- run node with perf enabled `perf record -e cycles:u -g -- node --perf-basic-prof app.js`
- disregard warnings about kernel
- run `perf script`, but pipe it through some cleanup: `perf script | egrep -v "( __libc_start| LazyCompile | v8::internal::| Builtin:| Stub:| LoadIC:|\[unknown\]| LoadPolymorphicIC:)" | sed 's/ LazyCompile:[*~]\?/ /' > perfs.out`
- `stackvis perf < perfs.out > flamegraph.htm`
- watch it burn :)

## Example

[TBD](tbd.md)
