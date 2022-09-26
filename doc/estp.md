===================================================
  Embedded Stack Trace Profiler (ESTP) User Guide
===================================================

.. default-role:: code
.. include:: rstcommon.rst

:Author: Andreas Rumpf
:Version: |nimversion|


Nim comes with a platform independent profiler -
the Embedded Stack Trace Profiler (ESTP). The profiler
is *embedded* into your executable. To activate the profiler you need to do:

* compile your program with the `--profiler:on --stackTrace:on`:option: command
  line options
* import the `nimprof` module
* run your program as usual.

You can in fact look at `nimprof`'s source code to see how to implement
your own profiler.

The setting `--profiler:on`:option: defines the conditional symbol `profiler`.
You can use `when compileOption("profiler")` to make the switch seamless.
If `profiler`:option: is `off`:option:, your program runs normally.
Otherwise your program is profiled.

```nim
when compileOption("profiler"):
  import nimprof
```

After your program has finished the profiler will create a
file ``profile_results.txt`` containing the profiling results.

Since the profiler works by examining stack traces, it's essential that
the option `--stackTrace:on`:option: is active! Unfortunately this means that a
profiling build is much slower than a release build.


Memory profiler
===============

You can also use ESTP as a memory profiler to see which stack traces allocate
the most memory and thus create the most GC pressure. It may also help to
find memory leaks. To activate the memory profiler you need to do:

* compile your program with the
  `--profiler:off --stackTrace:on -d:memProfiler`:option:
  command line options. Yes it's `--profiler:off`:option:.
* import the `nimprof` module
* run your program as usual.

Define the symbol `ignoreAllocationSize` so that only the number of
allocations is counted and the sizes of the memory allocations do not matter.


Example results file
====================

The results file lists stack traces ordered by significance.

The following example file has been generated by profiling the Nim compiler
itself: It shows that in total 5.4% of the runtime has been spent
in `crcFromRope` or its children.

In general the stack traces show you immediately where the problem is because
the trace acts like an explanation; in traditional profilers you can only find
expensive leaf functions easily but the *reason* why they are invoked
often remains mysterious.

::
  total executions of each stack trace:
  Entry: 0/3391 Calls: 84/4160 = 2.0% [sum: 84; 84/4160 = 2.0%]
    newCrcFromRopeAux
    crcFromRope
    writeRopeIfNotEqual
    shouldRecompile
    writeModule
    myClose
    closePasses
    processModule
    CompileModule
    CompileProject
    CommandCompileToC
    MainCommand
    HandleCmdLine
    nim
  Entry: 1/3391 Calls: 46/4160 = 1.1% [sum: 130; 130/4160 = 3.1%]
    updateCrc32
    newCrcFromRopeAux
    crcFromRope
    writeRopeIfNotEqual
    shouldRecompile
    writeModule
    myClose
    closePasses
    processModule
    CompileModule
    CompileProject
    CommandCompileToC
    MainCommand
    HandleCmdLine
    nim
  Entry: 2/3391 Calls: 41/4160 = 0.99% [sum: 171; 171/4160 = 4.1%]
    updateCrc32
    updateCrc32
    newCrcFromRopeAux
    crcFromRope
    writeRopeIfNotEqual
    shouldRecompile
    writeModule
    myClose
    closePasses
    processModule
    CompileModule
    CompileProject
    CommandCompileToC
    MainCommand
    HandleCmdLine
    nim
  Entry: 3/3391 Calls: 41/4160 = 0.99% [sum: 212; 212/4160 = 5.1%]
    crcFromFile
    writeRopeIfNotEqual
    shouldRecompile
    writeModule
    myClose
    closePasses
    processModule
    CompileModule
    CompileProject
    CommandCompileToC
    MainCommand
    HandleCmdLine
    nim
  Entry: 4/3391 Calls: 41/4160 = 0.99% [sum: 253; 253/4160 = 6.1%]
    updateCrc32
    crcFromFile
    writeRopeIfNotEqual
    shouldRecompile
    writeModule
    myClose
    closePasses
    processModule
    CompileModule
    CompileProject
    CommandCompileToC
    MainCommand
    HandleCmdLine
    nim
  Entry: 5/3391 Calls: 32/4160 = 0.77% [sum: 285; 285/4160 = 6.9%]
    pop
    newCrcFromRopeAux
    crcFromRope
    writeRopeIfNotEqual
    shouldRecompile
    writeModule
    myClose
    closePasses
    processModule
    CompileModule
    CompileProject
    CommandCompileToC
    MainCommand
    HandleCmdLine
    nim
  Entry: 6/3391 Calls: 17/4160 = 0.41% [sum: 302; 302/4160 = 7.3%]
    doOperation
    forAllChildrenAux
    pop
    newCrcFromRopeAux
    crcFromRope
    writeRopeIfNotEqual
    shouldRecompile
    writeModule
    myClose
    closePasses
    processModule
    CompileModule
    CompileProject
    CommandCompileToC
    MainCommand
    HandleCmdLine
    ...
    nim
  Entry: 7/3391 Calls: 14/4160 = 0.34% [sum: 316; 316/4160 = 7.6%]
    Contains
    isAccessible
    interiorAllocatedPtr
    gcMark
    markStackAndRegisters
    collectCTBody
    collectCT
    rawNewObj
    newObj
    newNode
    copyTree
    matchesAux
    matches
    resolveOverloads
    semOverloadedCall
    semOverloadedCallAnalyseEffects
    ...
    CommandCompileToC
    MainCommand
    HandleCmdLine