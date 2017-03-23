# Flame graphs

## What's a flame graph useful for?

Flame graphs are a way of visualizing CPU time spent in functions. They will help you pin down where you spend too much time doing synchronous operations.

## How to flame graph

no more Solaris vms for flame graphs on linux!

1. install lldb in your system (eg. with apt-get)
1. install llnode according to readme [llnode notes](llnode.md)
1. install linux-tools-common (eg. with apt-get)
1. try running `perf` - it might complain about missing kernel modules, install them too
1. install stackvis `npm i -g stackvis`
1. run node with perf enabled `perf record -e cycles:u -g -- node --perf-basic-prof app.js`
1. disregard warnings unless they're saying you can't run perf due to missing packages; you will get some warnings about not being able to access kernel module samples which you're not after anyway.
1. run `perf script`, but pipe it through some cleanup: `perf script | egrep -v "( __libc_start| LazyCompile | v8::internal::| Builtin:| Stub:| LoadIC:|\[unknown\]| LoadPolymorphicIC:)" | sed 's/ LazyCompile:[*~]\?/ /' > perfs.out` [explanation](#about-perf-script-filtering)
1. run `stackvis perf < perfs.out > flamegraph.htm`

and watch it burn :)

### Use perf to sample a running preprocess

```
perf record -F99 -p `pgrep -n node` -g -- sleep 3
```

Wait, what is that sleep 3 for? It's there to keep the perf running - despite `-p` option pointing to a different pid, the command needs to be executed on a process and end with it. That's just how perf works.

Why is `-F` (profiling frequency) set to 99? I honestly don't know, Netflix guys used that value. Results look good. You can adjust if you want.

After you get that 3 second perf record, proceed with generating the flame graph with last two steps from above.

### About `perf script` filtering

The long line of greps and seds after `perf script` call is there to remove some V8 and node internals so that it's easier for you to focus on your JavaScript calls. These are only important if you are looking for an issue with Node internals.

If you read your flame graph and it seems odd, as if something is missing in the key function taking up most time, try generating your flame graph without the filters - maybe you got a rare case of an issue with Node itself.

### Node's profiling options

`--perf-basic-prof-only-functions` and `--perf-basic-prof` seem like the only two you might be initially interested in for debugging your JavaScript code. I'm not even going to link to more advanced stuff here. Profiling Node itself is outside the scope of this howto.

`--perf-basic-prof-only-functions` produces less content, so it's the option with least overhead.

Why do I need them at all?

Well, without these options you'll still get a flame graph, but with most bars labeled `v8::Function::Call`. 

## Examples

See a real flame graph - download the file from /samples/flame-graph-example.html

Or even better, practice capturing it yourself with /samples/flame-graph-exercise [flame graph exercise](./samples/flame-graph-exercise)
