Canary
======

Canary is a set of tools built on a unification-based alias analysis.
Relative papers include "Points-to analysis in almost linear time", 
"Fast algorithms for Dyck-CFL-reachability with applications to alias 
analysis", "LEAP: lightweight deterministic multi-processor replay of 
concurrent java programs", "Persuasive prediction of concurrency 
access anomalies", etc. You can read them for details.

We have built and tested it on *32-bit* x86 linux architectures using
gcc 4.8.2 and llvm-3.6.

If you use Canary, please cite Canary as below (latex style). 

> \footnote{Canary. \url{https://github.com/qingkaishi/canary}}


Building Canary
------

* Build via Scons

```bash
git clone https://github.com/qingkaishi/canary.git
cd canary
scons # >= 2.3.0
sudo scons install
```

* Build via Cmake

```bash
git clone https://github.com/qingkaishi/canary.git
cd canary
mkdir build
cd build
cmake ..
make
```

Using Canary
------

**Using Alias Analysis**

```bash
# NOTE: do not use -fvectorize and related options when compile source codes,
# since canary now does not support them
clang -c -emit-llvm -O2 -g <src_file> -o <bitcode_file>
canary <bitcode_file> -o <output_file>
```

Or you can build a shared library (you need to modify the Makefile yourself), 
and use the following equivalent commands.

```bash
clang -c -emit-llvm -O2 -g <src_file> -o <bitcode_file>
opt -load dyckaa.so -lowerinvoke  -dyckaa -basicaa  <bitcode_file> -o <output_file>
```

* -print-alias-set-info
This will print the evaluation of alias sets and outputs all alias sets, and their 
relations (dot style).

* -count-fp
Count how many functions that a function pointer may point to.

* -no-function-type-check
If the option is set, we do not check the function type when resolving pointer
calls, otherwise, only type compatible function can be aliased with a function
pointer.

NOTE: currently, f1 and f2 are two type compatible functions iff.

> Both or netheir of them are var arg function;

> Both or netheir of them have a non-void return value;

> Same number of parameters;

> Same type store sizes of each pair of parameters;

> There is an explicit cast operation between type(f1) and type(f2) 
(it works with option -with-function-cast-comb).

* -dot-dyck-callgraph
This option is used to print a call graph based on the alias analysis.
You can use it with -with-labels option, which will add lables (call insts)
to the edges in call graphs.

* -preserve-dyck-callgraph
Preserve the call graph for later usage. Only using  -dot-dyck-callgraph
will not preserve the call graph.

* -leap-transformer
A transformer for LEAP. Please read ``LEAP: lightweight deterministic 
multi-processor replay of concurrent java programs". Here is an example.

```bash
# transform
canary -preserve-dyck-callgraph -leap-transformer <bitcode_file> -o <output_file>
# link a record version
clang++ <ouput_file> -o <executable> -lleaprecord
# execute it
# link a replay version
clang++ <ouput_file> -o <executable> -lleapreplay
# now you can replay
```

* -trace-transformer
A transformer for Pecan. Please read "Persuasive prediction of concurrency 
access anomalies". Here is an example.

```bash
canary -preserve-dyck-callgraph -trace-transformer <bitcode_file> -o <output_file>
# link a record version 
clang++ <ouput_file> -o <executable> -ltrace
# a log file will be produced after executing it; using the following command
# to analyze it.  
pecan <log_file> <result_file>
```

NOTE
-----
Transformers and corresponding supports are not updated in time.

Bugs
------

Please help us look for bugs. Please feel free to contact Qingkai.
Email: qingkaishi@gmail.com

TODO
------
* Upgrade Canary:

> 1. inter-proceduare analysis: performance optimization

