# Notes on llnode and lldb installation

## What's all this?

### lldb

It's a tool for analyzing low level diagnostics information from processes. Node.js in our case :)

### llnode

It's a plugin to lldb that shows real names of references/functions in JavaScript in lldb (normally you'd only get names for C++ functions and objects)

It's great that lldb+llnode can be used to read diagnostics from node, it used to be that only `mdb` on solaris based operating system could do that.

## Notes on installation

[https://github.com/nodejs/llnode](https://github.com/nodejs/llnode)

Use install instructions, `npm install -g llnode` doesn't seem to work yet.
But I had no trouble building according to instructions.

You'll need to install lldb too.



*It should work on all flavors of ubuntu and most other distros with any version of lldb starting at 3.8. If you find out more, feel free to PR here*

*On 2017-02-03 I managed to install lldb-3.8 with latest llnode in xubuntu 16.04.1 kernel 4.4.0-62-generic x86_64*
