# Node.js diagnostics - howtos


## Table of contents

| Get started |
| --- |
| tbd: [how to choose which tool to use](tbd.md) |
|**Guides**|
| [about llnode](llnode.md) |
| [flame graphs](flame-graphs.md) |
| wip: [core dumps](core-dumps.md) |
| wip: [tracking (de)optimizations](optimizations.md) |
| tbd: [heap dumps](tbd.md) |
| **External links** |
| [read this to really understand how asynchronous code runs](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/) |

----
### Goal

The purpose of this repo is to collect all the intricate bits of knowledge about diagnosing what happens to node applications' performance, memory and production runtime.

- short documents
- link only to resources that should stay up
- make recognizing what's outdated easy
- make it useful as notes for someone who knows it already but needs details to be able to teach others without preparation

---


- Why does it focus on tools for Linux?
  - Because people tend not to run web applications in production on Mac.
