# Memory

You have probably encountered memory instructions like `load` and `store` in Bril programs,
 and so far we have mostly ignored them or worse, said that they have prevented other optimizations.

Resources:
- [15-745 slides](https://www.cs.cmu.edu/afs/cs/academic/class/15745-s19/www/lectures/L13-Pointer-Analysis.pdf)


## Simple Memory Optimizations

Our motivating goal for this lesson is to be able to perform 
 some similar optimizations
 that we did on variables on memory accesses.
In the first couple lessons, we performed the following optimizations:
- **Dead code elimination** removed instructions that wrote to variables that were never read from (before being written to again).
- **Copy propagation** replaced uses of variables with their definitions.
- **Common subexpression elimination** prevented recomputing the same expression multiple times.

We can imagine similar optimizations for memory accesses. 
- **Dead store elimination** removes stores to memory that are never read from.
  Like our initial trivial dead code elimination, this is simplest to recognize 
  when you have two stores close to each other.
     - Just like `x = 5; x = 6;` can be replaced with `x = 6;`...
     - We can replace `store x 5; store x 6;` with `store x 6;`.
- **Store-to-load forwarding** replaces a load with the most recent store to the same location.
  This is similar to copy propagation, but for memory.
     - Just like `x = a; y = x;` can be replaced with `x = a; y = a`...
     - We can replace `store x 5; y = load x;` with `store x 5; y = 5;`.
- **Redundant load elimination** works on a similar redundancy-elimination principle to common subexpression elimination.
     - Just like `x = a + b; y = a + b;` can be replaced with `x = a + b; y = x;`...
     - We can replace `x = load p; y = load p;` with `x = load p; y = x;`.


As written, 
 these optimizations are not very useful
 since they only apply when the memory accesses are right next to each other.
How can we extend them?

To extend them, 
 we first need to see
 how we could go wrong.
In each of these cases,
 an intervening memory instruction would make the optimization invalid.
For example, say we are trying to do dead store elimination on the following code:
```
store x 5;
...
store x 6;
```

Whether or not we can eliminate the first store depends on what happens between the two stores.
In particular,
 a problematic instruction would be a load from `x`,
 since it would observe the value `5`  that we are trying to eliminate.

So a sound, very conservative approach would be to say that we can only perform these optimizations
 if we can prove that there are **no** intervening memory instructions at all.

What about a load from some other pointer `y`?
That might break the optimization, but it might not!
It could be the case that `y` is actually an *alias* for `x`,
 and so the load from `y` would observe the value `5` and break the optimization.
Or, `y` could be completely unrelated to `x`,
 in which case we could still eliminate the first store!

## Alias Analysis

This is the essence of a huge field of compilers/program analysis research called *alias analysis*.
The purpose of such an analysis is to answer one question:
 can these two pointers (`x` and `y` in the above) in the program refer to the same memory location?

If we can prove they cannot (they don't alias),
 then we can perform the above optimization and many others,
 since we don't have to worry about interactions between the two pointers.
If we can't prove they don't alias,
 then we have to be conservative and assume they do alias,
 which will prevent us from performing many optimizations.

There are many, many algorithms for alias analysis
 that vary in their precision, efficiency, and assumptions about the memory model.
Exploring these would make for a good project!

We will present a simple alias analysis powered by, what else, dataflow analysis!
The idea is to track the memory locations that each pointer can point to,
 and then use that information to determine if two pointers can point to the same location.

### Memory Locations

What's a memory location?
In some languages, like C with its address-of operator `&`,
 you can get a pointer to a memory location in many ways.
But in Bril,
 there is only one way to create a memory location,
 calling the `alloc` instruction.
The `alloc` instruction _dynamically_ returns a fresh pointer 
 to a memory location that is not aliased with any other memory location.

How can we model that in a static analysis?
We can't!
So we will be conservative 
 and "name" the memory locations according to the (static) program location where they were allocated.
This is a standard approach in alias analysis,
 since it allows you to work with a finite set of memory locations.

So for example, if we have the following program:
```c
while (...) {
    x = alloc 10; // line 1
    y = alloc 10; // line 2
}
```

We now can statically say what `x` points to: the memory location allocated at line 1.
This is a necessary approximation to avoid having to name the potentially infinite number of memory locations that could be allocated in a loop.
Note how we can still reason about the non-aliasing of `x` and `y` in this case.

### A Simple Dataflow Analysis

While `alloc` is the only way to create a memory location from nothing,
 there are other ways to create memory locations, and these are the sources of aliasing.

- `p1 = id p2`: the move or copy instruction is the most obvious way to create an alias.
- `p1 = ptradd p2 offset`: pointer arithmetic is an interesting and challenging source of aliasing.
  To figure out if these two pointers can alias, we'd need to figure out if `offset` can be zero.
  To simplify things, we will assume that `offset` could always be zero, 
  and so we will conservatively say that `p1` and `p2` can alias.
  This also means we do not need to include indexing informations into our represetation of memory locations.
- `p1 = load p2`: you can have pointers to other pointers, and so loading a pointer effectively copy a pointer, creating an alias.

Our dataflow analysis 
 will center around building the _points-to_ graph,
 a structure that maps each variable to the set of memory locations it can point to.

Here is a first, very conservative stab at it:

- **Direction**: forward
- **State**: map of `var -> set[memory location]`
    - If two vars have a non-empty intersection, they might alias!
- **Meet**: Union for each variable's set of memory locations
- **Transfer function**: 
    - `x = alloc n`: `x` points to this allocations
    - `x = id y`: `x` points to the same locations as `y` did
    - `x = ptradd p offset`: same as `id` (conservative)
    - `x = load p`: we aren't tracking anything about p, so `x` points to all memory locations
    - `store p x`: no change

This is a very conservative but still useful analysis!
If your program doesn't have any pointers-to-pointers,
 then this big approximation of not modeling what `p` might point to isn't so bad. 

This is, however, still an _intra_-procedural analysis.
So we don't know anything about the incoming function arguments.
Ensure that you initialize your analysis something conservative, 
 like all function arguments pointing to all memory locations.

# Task

Implement an alias analysis for Bril programs
 and use it to perform at least one of the memory optimizations we discussed above.

You may implement any alias analysis you like,
 including the simple one described above.
If you are interested in a more advanced analysis,
 you could look into the _Andersen_ analysis,
 or the more efficient but less precise _Steensgaard_ analysis.

As always, run your optimizations on the provided test cases
 and make sure they pass.
Show some data analysis of the benchmarking results.

In addition,
 create at least one contrived example that shows your optimization in action.
It can be a simple code snippet,
 but show the before and after of the optimization.
