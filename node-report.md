# The node-report module

## What does node-report do?

The node-report module produces a human-readable diagnostic summary, written to file. So it's a lightweight dump, with key information extracted from the Node.js/V8 runtime, plus information from the native platform/process.

The report is intended for development, test and production use, to capture and preserve information for subsequent problem determination. It includes JavaScript and native stack traces, V8 heap statistics, platform information and resource usage etc. With the node-report module enabled, reports can be triggered:
  - automatically on unhandled exceptions and fatal errors
  - by sending a signal to the process
  - by calling a JavaScript API.

https://www.npmjs.com/package/node-report

https://github.com/nodejs/node-report

## Usage
```
$ npm install node-report
$ node -r node-report my_application.js
```
A report will then be produced automatically if an unhandled exception or fatal error event occurs in the Node.js application. To see an example report, produced on an uncaught exception, you can simply run a non-existent application with node-report enabled:
```
$ node -r node-report foo.js
```

For a running application, you can trigger a report when required by sending a USR2 signal to the process. First start the application, then in a separate terminal window, find the ID of the Node.js process, and send the signal to it. The node-report module will handle the signal and produce a report, and the application will continue to run:
```
$ node -r node-report my_application.js

$ ps -ef | grep node
user 14530 9292 0 16:36 pts/5   00:00:00 node -r node-report simple

$ kill -USR2 14530
```

On the terminal window for the Node.js process you will see confirmation that a report has been produced:
```
Writing Node.js report to file: node-report.20170424.163619.14530.00.txt
Node.js report completed
```

You can also trigger a report via an API call from a JavaScript application. You could add this code into an error path in your application:

```
var nodereport = require('node-report');
nodereport.triggerReport();
```

## Example of a report

```
================================================================================
==== Node Report ===============================================================

Event: exception, location: "OnUncaughtException"
Filename: node-report.20170524.165021.14658.001.txt
Dump event time:  2017/05/24 16:50:21
Module load time: 2017/05/24 16:50:21
Process ID: 14658
Command line: node -r node-report foo 

Node.js version: v6.10.0
(ares: 1.10.1-DEV, http_parser: 2.7.0, icu: 58.2, modules: 48, openssl: 1.0.2k, 
 uv: 1.9.1, v8: 5.1.281.93, zlib: 1.2.8)

node-report version: 2.1.2 (built against Node.js v6.10.0, glibc 2.23, 64 bit)

OS version: Linux 4.4.0-78-generic #99-Ubuntu SMP Thu Apr 27 15:29:09 UTC 2017
(glibc: 2.23)

Machine: ThinkCentre-M57p x86_64

================================================================================
==== JavaScript Stack Trace ====================================================

Function.Module._resolveFilename (module.js:471:5)
Function.Module._load (module.js:417:25)
Module.runMain (module.js:604:10)
run (bootstrap_node.js:394:7)
startup (bootstrap_node.js:149:9)
bootstrap_node.js:509:3

================================================================================
==== Native Stack Trace ========================================================

 0: [pc=0x7f8f0a642663] nodereport::TriggerNodeReport(v8::Isolate*, nodereport::DumpEvent, char const*, char const*, char*, v8::MaybeLocal<v8::Value>) [/home/test/node_modules/node-report/api.node]
 1: [pc=0x7f8f0a6446ab] nodereport::OnUncaughtException(v8::Isolate*) [/home/rchamberlain/test/node_modules/node-report/api.node]
 2: [pc=0xd503f3] v8::internal::Isolate::Throw(v8::internal::Object*, v8::internal::MessageLocation*) [node]
 3: [pc=0xeb1823] v8::internal::Runtime_Throw(int, v8::internal::Object**, v8::internal::Isolate*) [node]
 4: [pc=0xd26918092a7] 

================================================================================
==== JavaScript Heap and Garbage Collector =====================================

Heap space name: new_space
    Memory size: 1,048,576 bytes, committed memory: 982,920 bytes
    Capacity: 1,031,680 bytes, used: 966,024 bytes, available: 65,656 bytes
Heap space name: old_space
    Memory size: 2,052,096 bytes, committed memory: 1,470,360 bytes
    Capacity: 1,436,632 bytes, used: 1,436,568 bytes, available: 64 bytes
Heap space name: code_space
    Memory size: 2,097,152 bytes, committed memory: 630,528 bytes
    Capacity: 583,360 bytes, used: 581,376 bytes, available: 1,984 bytes
Heap space name: map_space
    Memory size: 1,130,496 bytes, committed memory: 205,216 bytes
    Capacity: 171,424 bytes, used: 171,424 bytes, available: 0 bytes
Heap space name: large_object_space
    Memory size: 0 bytes, committed memory: 0 bytes
    Capacity: 1,491,021,312 bytes, used: 0 bytes, available: 1,491,021,312 bytes

Total heap memory size: 6,328,320 bytes
Total heap committed memory: 3,289,024 bytes
Total used heap memory: 3,155,392 bytes
Total available heap memory: 1,491,089,016 bytes

Heap memory limit: 1,501,560,832

================================================================================
==== Resource Usage ============================================================

Process total resource usage:
  User mode CPU: 0.060000 secs
  Kernel mode CPU: 0.000000 secs
  Average CPU Consumption : 6%
  Maximum resident set size: 23,724,032 bytes
  Page faults: 0 (I/O required) 1857 (no I/O required)
  Filesystem activity: 0 reads 16 writes

Event loop thread resource usage:
  User mode CPU: 0.060000 secs
  Kernel mode CPU: 0.000000 secs
  Average CPU Consumption : 6%
  Filesystem activity: 0 reads 16 writes

================================================================================
==== Node.js libuv Handle Summary ==============================================

(Flags: R=Ref, A=Active)

Flags  Type      Address
[-A]   async     0x1e03020
[-A]   async     0x284f300
[--]   check     0x284f520
[R-]   idle      0x284f598
[--]   prepare   0x284f688
[--]   check     0x284f700
[--]   idle      0x284f610
[-A]   async     0x7f8f0a849c80

================================================================================
==== System Information ========================================================

Environment variables
  XDG_VTNR=7
  XDG_SESSION_ID=c2
  CLUTTER_IM_MODULE=xim
  SESSION=ubuntu
  SHELL=/bin/bash
  TERM=xterm-256color
  ...etc

Resource limits                        soft limit      hard limit
  core file size (blocks)                       0       unlimited
  data seg size (kbytes)                unlimited       unlimited
  file size (blocks)                    unlimited       unlimited
  max locked memory (bytes)                 65536           65536
  max memory size (kbytes)              unlimited       unlimited
  open files                                65536           65536
  stack size (bytes)                      8388608       unlimited
  cpu time (seconds)                    unlimited       unlimited
  max user processes                        18630           18630
  virtual memory (kbytes)               unlimited       unlimited

Loaded libraries
  /lib/x86_64-linux-gnu/libdl.so.2
  /lib/x86_64-linux-gnu/librt.so.1
  /usr/lib/x86_64-linux-gnu/libstdc++.so.6
  /lib/x86_64-linux-gnu/libm.so.6
  /lib/x86_64-linux-gnu/libgcc_s.so.1
  ...etc

================================================================================
```

