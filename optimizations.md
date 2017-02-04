# Optimizations

## How/why node optimizes and deoptimizes functions

v8 compiles functions and tries to optimize them to better use memory and perform less unnecessary checks.
For the most part it tracks the types of your variables and compiles functions into strongly typed code assuming you won't change your mind, until you call the function with arguments of different types - that's when deoptimization happens.

https://community.risingstack.com/how-to-find-node-js-performance-optimization-killers/

It can also bail out from optimizing a function for many reasons.

https://github.com/vhf/v8-bailout-reasons

## How to trace optimizations?

```
node --trace-opt
node --trace-deopt
node --trace-opt --trace-deopt app.js | grep someFunctionName
```
