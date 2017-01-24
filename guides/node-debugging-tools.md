---
layout: page
type: guide
title: Node debugging tools
permalink: /guides/node-debugging-tools/
---

# Node debugging tools

## Notes

Tools are not unified, they come from different places

Typical debugging process, starts high-level and goes lower

1. console.log();
2. node --inspect to check javascript, heap snapshot
3. llnode or profiling from C++ end, or inspecting heap

* _[LLDB](http://lldb.llvm.org/): C/C++ debugger for Xcode_
* _[MDB](https://docs.oracle.com/cd/E18752_01/html/816-5041/chapter-8.html): C/C++ debugger for Solaris_
* _Core dump: snapshot of entire memory blob_
* _Post-mortum debugging: after crash, inspect the core-dump (entire C heap & Javascript). Good for memory leaks, but most day-to-day node work, this should be last resort tool_

## Node tick processor profiling [link](https://firebearstudio.com/blog/node-js-prof-profiling-node-js-applications.html)

```
node --prof 
```

* For profiling

## llnode [link](https://github.com/nodejs/llnode)

A [lldb](http://lldb.llvm.org/) plugin for low level debugging

* Intially, MDB not available on mac/linux only solaris
* C++ plugin for lldb
* Supported by IBM guy & Alibaba
* Mostly post-mortum, some live-debugging
* to see C++ WITH Javascript 
* node lldb plugin can inspect any memory address, see request headers, all heap objects etc
* Note: not only stack trace, full stack: pointer stack & js stack frames (like objects w/different propertiess e.g. callee)
* v8 exports debug constants
* Probably will not add profiling

## node inspector [link](https://github.com/node-inspector/node-inspector)

* old seperate binary
* used debug port for everything, not debug protocol so it would keep breaking

## node inspect 

```
node --inspect
```

* add backend to talk to Chrome debugger 
* Profiling etc
* CLI frontend to node-inspector 
* see only javascript debugging

