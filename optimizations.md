# Optimizations

## How/why node optimizes and deoptimizes functions

v8 compiles functions and tries to optimize them to better use memory and perform less unnecessary checks.
For the most part it tracks the types of your variables and compiles functions into strongly typed code assuming you won't change your mind, until you call the function with arguments of different types - that's when deoptimization happens.

It can also bail out from optimizing a function for many reasons.

*This article is valid for Node.js with V8 engine. There are ongoing efforts to run multiple other JavaScript engines in Node, which might approach optimizations differently*

## How to trace (de)optimizations?

```
node --trace-opt
node --trace-deopt
node --trace-opt --trace-deopt app.js | grep someFunctionName
```

With `--trace-opt` and `--trace-deopt` flags node process will print detailed logs of what the compiling/optimizing pipeline is doing while the code is running.

See examples below for what it could look like on a V8 engine, filtered to a single function.

*2017/06 chakracore stable build doesn't seem to support these flags*
*Engines other than V8 will probably support these flags, but expect vastly different information in the output*

### Testing for bailouts

*This is V8 specific*

If you identify a function your code heavily depends on and calls very often, you might want to assert that it doesn't change to be non-optimizable. In order to do that, you can use the `%GetOptimizationStatus()` native V8 function.
To gain access to it, run node with `--allow-natives-syntax`

```js
function printStatus(fn) {
  switch(%GetOptimizationStatus(fn)) {
    case 1: console.log('optimized'); break;
    case 2: console.log('not optimized'); break;
    case 3: console.log('always optimized'); break;
    case 4: console.log('never optimized'); break;
    case 6: console.log('maybe deoptimized'); break;
  }
}

// Call your function once to pre-populate information used in Optimization
myFunction(1);
// Order an optimization attempt
%OptimizeFunctionOnNextCall(myFunction);
// Run again
myFunction(1);
// Check status of optimization
printStatus(myFunction);
```

Checking optimization is available as npm packages
- [check-v8-optimised](https://www.npmjs.com/package/check-v8-optimised)
- [chai-v8-optimisation](https://www.npmjs.com/package/chai-v8-optimisation)

## More reading

Most commonly referenced sources on de-optimization and non-optimization
- https://github.com/petkaantonov/bluebird/wiki/Optimization-killers
- https://github.com/vhf/v8-bailout-reasons

## Examples

Practice finding and fixing deoptimizations with [a deopt exercise](https://github.com/naugtur/node-example-deopt)

### Optimization logs for a single function
```
[marking 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> for optimized recompilation, reason: small function, ICs with typeinfo: 6/8 (75%), generic ICs: 0/8 (0%)]
[compiling method 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> using Crankshaft]
[optimizing 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> - took 0.156, 0.486, 0.071 ms]
[completed optimizing 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)>]
[deoptimizing (DEOPT soft): begin 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> (opt #48) @7, FP to SP delta: 24, caller sp: 0x7ffc8205b7c0]
            ;;; deoptimize at 12733: Insufficient type feedback for generic named access
  reading input frame Buffer.toString => node=57, args=1, height=5; inputs:
      0: 0x3f4ef4310301 ; [fp - 16] 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)>
      1: 0x1eac35f701c1 ; [fp + 16] 0x1eac35f701c1 <an Uint8Array with map 0x3ae84081aaf1>
      2: 0x3f4ef43bb5a9 ; [fp - 24] 0x3f4ef43bb5a9 <FixedArray[40]>
      3: argumets object #0 (length = 0)
      4: 0x42f8a804311 ; (literal 2) 0x42f8a804311 <undefined>
      5: 0x1eac35f701c1 ; [fp + 16] 0x1eac35f701c1 <an Uint8Array with map 0x3ae84081aaf1>
      6: 0x3f4ef430e911 ; rax 0x3f4ef430e911 <JS Function utf8Slice (SharedFunctionInfo 0x3f4ef430e869)>
  translating frame Buffer.toString => node=57, height=32
    0x7ffc8205b7b8: [top + 64] <- 0x1eac35f701c1 ;  0x1eac35f701c1 <an Uint8Array with map 0x3ae84081aaf1>  (input #1)
    0x7ffc8205b7b0: [top + 56] <- 0x1473b024a7c3 ;  caller's pc
    0x7ffc8205b7a8: [top + 48] <- 0x7ffc8205b7d8 ;  caller's fp
    0x7ffc8205b7a0: [top + 40] <- 0x3f4ef43bb5a9 ;  context    0x3f4ef43bb5a9 <FixedArray[40]>  (input #2)
    0x7ffc8205b798: [top + 32] <- 0x3f4ef4310301 ;  function    0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)>  (input #0)
    0x7ffc8205b790: [top + 24] <- 0x42f8a8044f1 ;  0x42f8a8044f1 <Odd Oddball: arguments_marker>  (input #3)
    0x7ffc8205b788: [top + 16] <- 0x42f8a804311 ;  0x42f8a804311 <undefined>  (input #4)
    0x7ffc8205b780: [top + 8] <- 0x1eac35f701c1 ;  0x1eac35f701c1 <an Uint8Array with map 0x3ae84081aaf1>  (input #5)
    0x7ffc8205b778: [top + 0] <- 0x3f4ef430e911 ;  0x3f4ef430e911 <JS Function utf8Slice (SharedFunctionInfo 0x3f4ef430e869)>  (input #6)

[deoptimizing (soft): end 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> @7 => node=57, pc=0x1473b0340425, caller sp=0x7ffc8205b7c0, state=TOS_REGISTER, took 0.318 ms]
[evicting entry from optimizing code map (notify deoptimized) for 0x42f8a8e4779 <SharedFunctionInfo Buffer.toString>]
[marking 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> for optimized recompilation, reason: hot and stable, ICs with typeinfo: 9/8 (112%), generic ICs: 0/8 (0%)]
[compiling method 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> using Crankshaft]
[optimizing 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)> - took 0.119, 0.281, 0.095 ms]
[completed optimizing 0x3f4ef4310301 <JS Function Buffer.toString (SharedFunctionInfo 0x42f8a8e4779)>]
```
