# Local Optimization

We are going to start by discussing some 
 basic optimizations that you'll
 definitely want to do in your compiler.

## Dead code elimination

Let's start with dead code elimination as a motivating example.

"Dead code" is code that doesn't affect the "output" of a program.
Recall our discussion from [lesson 0](00-overview.md) 
 about the "obervability" of the "output" of a program
 (sorry about all the scare quotes).
In fact,
 we'll see today that the output of a (part of a) program
 is a tricky concept to pin down,
 and we'll constrain ourselves to local optimizations
 for now.

Let's start with a simple example:

```c
int foo(int a) {
	int x = 1;
	int y = 2;
	x = x + a;
	return x;
}
```

Typically, 
 we won't be working with the surface syntax of a program,
 we'll be working with a lower-level intermediate representation (IR).
Most (including the one we'll use)
 are oriented around a sequence of instructions.

Here's the same code in instruction form:
```
x = 1
y = 2
x = x + a
return x
```

In this example, the assignment to variable `y` is dead code.
It doesn't affect the output of the program,
 which is here clearly constrained as a straight line function
 that outputs a single value `x`.

Let's make things a bit more interesting:

```c
int foo(int a) {
	int x = 1;
	int y = 2;
	if (x > 0) {
		y = x;
	}
	x = x + a;
	return x;
}
```

And in instruction form:
```
	x = 1
	y = 2
	c = x > 0
	branch c L1 L2
L1: 
	y = x
L2: 
	x = x + a
	return x
```

Can you think of a way to eliminate the dead code at label `L1`?

Let's start by defining what we mean by "dead code"
 a bit more precisely so as to avoid the observability issue.
For now, an instruction is dead if both
- it is pure (has no side effects other than its assignment)
- its result is never used

For today,
 we'll only look at instructions that are pure;
 so no calls, no stores, etc.
Handling those will pose a challenge to the algorithms 
 we'll see today and we'll defer them to later.

It remains to show that a variable is never used.
A conservative approach (overapproximation is theme we'll see a lot)
 is to say that a variable is used if 
 there is no instruction that uses it.

Here's a simple algorithm to eliminate dead code
 based on this definition:
```py
used = {}
for instr in func:
	used += instr.args

for instr in func:
	if instr.dest not in used and is_pure(instr):
		del instr
```

Great! We've eliminated the dead code in the above example.

Can you think of some examples where this algorithm would fail
 to eliminate all the dead code?

Here are some different ways our code might be incomplete:
1. There's more dead code! This can be resolved by iterating until convergence.
	```
	a = 1
	b = 2
	c = a + a
	d = b + b
	return c
	```
2. There's a "dead store" (a variable is assigned to but never used).
	```
	a = 1
	a = 2
	return a
	```
3. A variable is used _somewhere_, but in a part of the code that won't actually run.
	```c
	int a = 1;
	int b = ...;
	if (a == 0) {
		use(b);
	}
	```

We handled the first case by simply iterating our algorithm.
The third case is a bit more challenging, and we'll see how to handle it later in the course.

How about the second case?
The third case hints that reasoning about control flow is important.
So perhaps we can make our lives easier by reasoning not doing that!
It's clear that the example in 2 is "easy" in some sense
 because we are looking at straight-line code.
So let's try to make our lives easier by looking _only_ at straight-line code,
 even in the context of a larger program.

## Control flow graphs

We'd like to reason about straight-line code whenever possible.
But even simple programs have instructions that break up this straight-line flow.

A control flow graph (CFG) is a way to represent the flow of control in a program.
It's a directed graph where the nodes 
 are "atomic" blocks of code
 the edges represent control flow between them.

One way to construct a CFG is to place every instruction in its own block.
Blocks are connected by edges
 to the instructions that might follow them.
A simple assignment instruction 
 will have one _successor_,
 one out-going edge to the next instruction.
A jump will also have one successor, to the target of the jump.
A branch will have two successors, one for each branch.

Blocks in a control flow graph are defined by a 
 "single-entry, single-exit" property.
That is, 
 there is only one point at which to enter the block (the top)
 and one point at which to exit the block (the bottom).
In other words, 
 if any part of a block is executed,
 the whole block is executed.

When we talk about basic blocks,
 in a CFG,
 we typically mean _maximal_ basic blocks.
These are blocks that are as large as possible
 while still maintaining the single-entry, single-exit property.
In our instruction-oriented IR,
 this means that a basic block is a sequence of instructions
 that may end with a _terminator_ instruction: a jump, branch, or return instruction (the exit point).
Terminators may _not_ appear in the middle of a basic block.

Here's our example from before again:
```
	x = 1
	y = 2
	c = x > 0
	branch c L1 L2
L1: 
	y = x
L2: 
	x = x + a
	return x
```

In this example, the basic blocks are the entry block, `L1`, and `L2`.
Why can't `L1` be combined with `L2`?
After all, `L1` only has one successor, can't it be folding into `L2`?
No, that would violate the single-entry property on the resulting block.
We want to guarantee that all of the instructions in a block are executed together or not at all.

## Local Dead Code Elimination

Now that we are equipped with control flow graphs
 with maximal basic blocks,
 we can focus on it as a unit of analysis.

Recall our problem case for dead code elimination:
```
a = 1
a = 2
return a
```

Our global algorithm failed to eliminate the dead code (the first assignment to `a`) in this case.


```py
unused: {var: inst} = {}
for inst in block:
	# if it's used, it's not unused
	for use in inst.uses:
		del unused[use]
	if inst.dest 
		# if this inst defines something
		# we can kill the unused definition
		if unused[inst.dest]:
			remove unused[inst.dest]
		# mark this def as unused for now
		unused[inst.dest] = inst
```

Be careful to process uses before defs (in a forward analysis like this one).
Otherwise, instructions like `a = a + 1` will be cause problems.

Note that we still have to iterate until convergence.

Is this as good as our global algorithm above?
Certainly it's better in some use cases (like the "dead store" example).

No! Consider this case with clearly dead instruction, but one that isn't "clobbered" by a later instruction:

```c
int foo(int a) {
	if (a > 0) {
		int x = 1;
	}
	return 0
}
```

So you need both for now.

## Local Value Numbering

Subsumes
- Copy propagation
- CSE
- Modular: can be extended to reason about constants, etc.
- Could be extended to do some DCE
	- And even more aggressive DCE once we have a way to figure out what vars are used later!

dce
```
a = 1
a = 2
return a
```

copy propagation
```
a = 1
b = a
c = b
return c
```

cse
```
x = a + b
y = a + b
z = x + y
return z
```

How does LVN accomplish all of this?
These optimizations can be see as reasoning about the "final value" 
 of the block of code,
 and figuring out a way to compute that value with fewer instructions.
The problem with reasoning about the "final value" is that
 the instruction-based IR doesn't make it easy to see what the "final value" is.

- Problem 1: variable name obscure the values being used
	- graphs can help with this, we will see this later in the course.
	- LVN's approach, "run" the program and see what values are used!
		- the graph will be part of a symbolic state that we keep around
- Problem 2: we don't know what variables will be used later in the program
	- later lecture


## Local Value Numbering

```py
values: dict[value, var] = {}
state: dict[var, value] = {}

def use(var) -> value:
	if var in state:
		return state[var]
	else:
		return Symbolic(var)

def set(var, value):
	# TODO doesn't handle clobbered vars
	if value in values:
		values[value] = var
	state[var] = value
	emit new instr

for inst in block:
	if inst.op == "const":
		set(inst.dest, inst.const_val)
	else:
		v = [inst.op] + [use(arg) for arg in inst.args]
		set(inst.dest, v)
```


on the fly
- reconstruct each instr based on the state at that time
- first time you see something: reconstruct basically that instr
- second time you see something: use an `id` instruction


Example:
```
a = 1
b = 2
c = 3
sum1 = a + b
sum2 = a + b
prod = sum1 * sum2
return prod
```


What about copy propagation?
- LVN is ignorant to the semantics of the instructions
- operations are "uninterpreted"
- easy enough to fix for copy propagation
```py
# instead of
# set(inst.dest, ["id", use(inst.args[0])])
if inst.op == "id":
	set(inst.dest, use(inst.args[0]))
```

add things like commutativity!
```
a = 1
b = 2
sum1 = a + b
sum2 = b + a
```

```py
if inst.op == "add":
	v = ["add"] + sorted([use(arg) for arg in inst.args])
	set(inst.dest, v)
```

how to do constant propagation?
- if `x = 5`, a use of `x` could be replaced with `5` instead of `x`.
- instructions need to be able to take constants as arguments
	- not always the case

constant folding
- how to evaluate through constants?


There is a bug!!

clobbered vars will overwrite the "saved" version of a computation
- introduce temps


- Does it do DCE?
	- No! It actually introduces a bunch of temp vars that might be dead.
	- We still need our (problematic) pass from before.
- How to do DCE with this?
	- Don't emit! Reconstruct this program fragment later.
	- Look at the block terminator... is it return? 
	- Then you can actually throw the rest of the symbolic state away!
	- Be careful with stateful operations.
	- Or use some analysis to figure out what vars are used later.

