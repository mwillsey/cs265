# Overview 

Welcome to CS 265: Code Generation and Optimization!

See the [syllabus](../syllabus.md) for logistics, policies, and course schedule.

## Course Goals

This is an advanced compilers class targeted at students who have experience building some level of compilers or interpreters.
We will focus on the "mid-end" of the compiler, which operates over an intermediate representation (IR) of the program.
The lines between front/mid/back-end don't have to be totally stark.
A compiler may have more than one IR (sometimes many, see the [Nanopass Compiler Framework](https://nanopass.org/)).
For the purpose of this class, let's define the "ends" of a compiler as follows.

- Front-end
	- Responsible for ingesting surface syntax, processing it, and translating it into an intermediate representation suitable for analysis and optimization
	- Examples: lexing, parsing, type checking, macro/template processing, elaborating language features into a reduced set of features.
- Mid-end
	- Many modern compilers include a "mid-end" portion of the compiler where analysis and optimization is focused.
	- The goal of the mid-end is to present a reduced surface area to make compiler tasks easier to work with.
	- Mid-ends are typically agnostic to details about the target machine.
- Back-end
	- The back-end is responsible for lowering the compiler's intermediate representation into something the target machine can understand. 
	- This layer is typically per target machine, so it will refer to specifics about the target machine architecture.
	- Examples: instruction selection, legalization, register allocation, instruction scheduling.

We will focus on the mid-end, 
 and we'll touch on some aspects of the backend. 
Compiler front-ends are not really going to be covered in this course.
You are free to incorporate any part of the compiler in your course [project](../project.md).

### Quick note: what's an optimization?

As the focus of this class is implementing compiler optimizations,
 it's worth defining what an optimization is.
In most other contexts, optimization is a process of finding the best solution to a problem.
In compilers,
 an optimization is more like an embetterment;
 it makes things "better" in some way
 but we often don't make any guarantees about how much better it is (or if it's better at all!)
 or how far from the best solution we are.
In other words,
 compiler optimizations are typically thought of as "best effort".
(Though there is the topic of superoptimization, which is a search for the best solution.)

Okay, so what's better?
For our purposes in this class, we'll define "better" as "faster".
And we'll define "faster" as "fewer instructions executed".
But of course, this definition of "faster" misses
 many details that will affect the actual runtime of a program
 on real hardware
 (e.g., cache locality, branch prediction, etc.).
We will touch on those topics, 
 but they are not the focus of this class
 as we are running our code on a simple virtual machine.
Faster may not be the only thing you care about as well.
You may care about code size, 
 or memory usage, 
 or power consumption,
 or any number of other things.
We will touch on some of these topics as well,
 but the course infrastructure is designed 
 to focus on the "fewer instructions executed" metric,
 as well as code size.

Also important, optimizations better preserve.... something!
But what?
The virtual machine in this class runs your program
 and not only returns the result of the program,
 but also returns some statistics about the program and its execution.
It's fair to say those are part of the interpreters semantics.
Do you want to preserve the result of the program?
So what's worth preserving?
Typically this discussion is framed in terms of "observable behavior".
This is useful, 
 since it captures the idea of preserving important side effects of the program.
For example,
 if you are optimizing an x86 program,
 you probably want to preserve not only its result but its effect on the status registers,
 memory, and other parts of the machine state that you consider "observable".
But you might not care about the cache state, 
 branch prediction state (or maybe you do care about this in a security context!), 
 etc.

Even the notion of preservation is not sufficient in many contexts.
Instead of a symmetric notion of equivalence,
 we may need to a _directional_ notion of equivalence.
Consider the following example:
 how would you relate `x / x` to `1`?
Are they equivalent? No!
Well, what are they then?
It goes back to your notion of "observable behavior".
If you consider division by zero to be an observable behavior,
 then `x / x` is not equivalent to `1`;
 unless you can prove that `x` is not zero via some analysis.
If you consider division by zero to be undefined behavior (like LLVM),
 then `x / x` is _still_ not equivalent to `1`.
Why not?
Well, you certainly would never want to replace
 the latter with the former,
 since that's clearly making the program "less defined" in some sense.
In these cases,
 we could say that `1` _refines_ `x / x`.
Refinement is a transitive, reflexive, but _not_ symmetric relation.
See this [blog post on Alive2](https://blog.regehr.org/archives/1722)
 for more info.

For this class, 
 we will mostly punt on these (important!) issues
 to aid you getting started as soon as possible.
We will mostly focus on executing fewer instructions,
 and we will look at code size as well.
In terms of preserving behavior,
 we will mostly care about preserving the result of the program.
Our programs "return" their result via printing,
 so we will have to care about effects like order of print statements.
Along the way,
 we will have to care about preserving effects like the state of memory.
On other effects like crashing,
 you may decide to preserve them or not.


## Topics Overview

Here are some of the planned topics for the course.

### Representing Programs

#### ASTs

There are many ways to represent a program! 
You are probably familiar with the abstract syntax tree (AST), 
 as that is a common representation many tools that interact with programs use.

There are many design decisions that go into choosing an AST representation.
These might affect memory layout, adding annotations to the tree, 
 or the ability reconstruct the concrete syntax from the tree.

Designing and working with ASTs is common and important! 
But it is not the focus of this class.
I'll do that part for you,
 so you can focus on working with the IR.

#### IRs

There's no formal definition of what an intermediate representation (IR) is;
 it's whatever is after the front-end and before the back-end.
Typically, IRs follow a few principles:
- They typically make explicit things like typing, sizing of values, and other
  details that might be implicit in the source language.
- They may reduce the number of language constructs to a smaller set.
	- For example, the front-end may support `if`s, `while`s, and `for`s, but the IR may only support `goto`s.
- They may also normalize the program into some form that is easier to analyze.
    - We will study Static Single Assignment (SSA) form later in class.

Very roughly, 
 IRs could be classified as either **linear** or **graph-based**.

A linear IR is one where the program is represented as a sequence of instructions.
These instructions 
 use and define values,
 and they mostly "run" one after the other,
 with some control flow instructions to jump around.
These IRs include some notion of a virtual machine that they run on:
 the Python and WebAssembly virtual machines are stack-based,
 the machine model for other IRs including LLVM and Cranelift are register-based.
Some, like the JVM, are a mix of both.
The virtual machine gives the IR an operational semantics.

A graph-based IR is one where the program is represented as a graph, of course.
The nodes in the graph represent values (or instructions),
 and the edges represent how values flow from one to another.
A simple dataflow graph is of course a graph-based IR,
 but it is limited to only representing computation of values.
More complex graph-based IRs can represent control flow as well,
 sometimes by including a machine model. 
Sometimes they do not require a machine model and can be denotationally defined.
A canonical graph-based IR 
 is the [Program Dependence Graph](https://dl.acm.org/doi/10.1145/24039.24041),
 and many IRs that it inspired
 including
 [Sea of Nodes](https://www.oracle.com/technetwork/java/javase/tech/c2-ir95-150110.pdf),
 [Program Expression Graphs](https://dl.acm.org/doi/10.1145/1480881.1480915),
 and
 [RVSDGs](https://dl.acm.org/doi/abs/10.1145/3391902).


Many IRs will mix these paradigms by grouping sets of instructions into blocks, 
 which are then organized into a graphically into a _control flow graph_.
In this model,
 the point is the group instructions into a block that can be reasoned about
 in a restricted way.
The blocks are then organized into the outer graph that captures how the blocks are connected.

In this class, we will focus on a "typical" IR that groups instructions into _basic blocks_.
The SSA form interacts with blocks in a particular way, 
 so we will spend some time understanding how to convert a program into SSA form.
If you are familiar with SSA form, 
 you may know the $\phi$-function, which is a way to merge values from different control flow paths.
Our IR will instead use _block arguments_ as a way to pass values between blocks.
This approach is taken in some modern compilers
 like [Cranelift](https://github.com/bytecodealliance/wasmtime/blob/main/cranelift/docs/ir.md),
 [MLIR](https://mlir.llvm.org/docs/LangRef/#blocks),
 and [Swift](https://github.com/swiftlang/swift/blob/main/docs/SIL.rst#basic-blocks).
If you aren't familiar with SSA form, 
 $\phi$-functions, or block arguments,
 don't worry! 
We will cover them in coming lessons.

You may choose to use a different IR for your work in this class!
We will mostly discuss in terms of a basic-block-oriented IR,
 but the concepts should be applicable to other IRs as well.

### Local Optimizations

We'll start by looking at local optimizations.
These are local in the sense that they operate on a single basic block at a time,
i.e., they don't reason about control flow.  

These include optimizations like constant folding, copy propagation, and dead code elimination.
We'll learn about value numbering and how it can subsume these optimizations.

### Transformation

As part of local optimizations,
 you'll probably add some _peephole optimizations_,
 a classic family of optimizations that look at a small set of instructions
 and replace them with something "better".

Examples of peephole optimizations include:
```
x * 2 -> x << 1
x + 0 -> x
```

This a big part of many compilers,
 so we will also discuss various frameworks to implement and reason about
 these optimizations.
You may choose to implement your optimization in such a framework,
 or you may choose to implement transformations directly.

### Static Analysis

We will quickly learn that many optimizations depend on knowing something about the program.
Much of the infrastructure in modern compilers is dedicated to proving facts about the program
 rather than transforming it.

We will learn about dataflow analysis, pointer analysis, and other static analysis techniques
 that can be used to prove facts about the program.
Like transformations, 
 these techniques can also be encompassed by some frameworks.
We will read about some of these frameworks,
 and you may choose to use them in your work or implement the analyses directly.

### Loop Optimizations

Many optimizations in compilers focus on loops,
 as they execute many times and are a common source of performance bottlenecks.
We will study some loop optimizations,
 but some will not be implementable in the course framework.
Many loop optimizations are focused on improving cache locality,
 such as loop interchange and loop tiling.
Our virtual machine does not model the cache,
 so these optimizations will not be measurable in the course framework.

### Other Stuff

Depending on time and interest,
 we may cover some other topics.
I would like to do something in the realm of automatic parallelization,
 either automatic vectorization or via a GPU-like SIMT model.








