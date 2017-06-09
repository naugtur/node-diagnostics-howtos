# Heap dumps

## What are heap dumps useful for?

Heap dump AKA heap snapshot is a file containing a snapshot of all memory allocated by your JavaScript running in Node.
They're useful for diagnosing memory consumption and finding memory leaks.

## How to get a snapshot file

```
npm install heapdump
```
[heapdump package](https://www.npmjs.com/package/heapdump)

Once installed, use one of:
- require the package in your app and run `kill -USR2 <pid of your process>`
- require the package and call its `.writeSnapshot` function

A file `heapdump-44214988.565232.heapsnapshot` is created. Numbers may vary.

## How to find a memory leak with heap dumps

1. Add `heapdump` to your app with means to run it in one of the ways described above
1. Let the process stabilize for a few seconds
1. Start using the functionality you suspect of leaking memory
1. Take first heap snapshot
1. Continue using the functionality for a while, preferably without running anything else in between
1. Take second heap snapshot
1. Open Chromium/Chrome dev tools and go to *Memory* tab
1. Load the older snapshot file first, newer one second
1. Select the newer snapshot and switch mode from *Summary* to *Comparison*
1. Look for large positive deltas and explore the references that caused them

Finding a real memory leak requires some getting used to the tools, so for best results, practice on some examples (below).

## Examples

Practice capturing heap dumps and finding memory leaks with [a heap dump exercise](https://github.com/naugtur/node-example-heapdump)
