# Lazy Code Motion

- [ACM DL PDF](https://dl.acm.org/doi/pdf/10.1145/143103.143136)

This work introduces lazy code motion, 
 a technique that sits in a larger family of optimization called _partial redundancy elimination_.
We've seen common subexpression elimination,
 which is a form of redundancy elimination that looks for expressions that are computed more than once.
In fact,
 loop invariant code motion is another form of (temporal) redundancy elimination.
Partial redundancy elimination is a more general form of this optimization, 
 and it can be seen to subsume most forms of LICM.

This work introduces a new form of partial redundancy elimination called _lazy code motion_,
 which is typically seen as simpler than the original PRE work by Morel and Renvoise.

Note that many practical LICM implementations are not technically 
 safe; they may actually hoist code out of a while-loop that doesn't have an effect.
This is making the assumption that the loop will actually run, 
 which is not always the case.
In practice, since you cannot always prove statically that a loop will run,
 you have to choose between being conservative and not hoisting anything,
 or being more aggressive and hoisting non-effectful loop-invariant code.
Partial redundancy elimination 
 techniques are typically more conservative than the sometime aggressive LICM
 implementations, which is why LICM isn't totally subsumed by PRE.

This paper is quite short but a little dense.
If you are feeling stuck, 
 focus your efforts on the transformation and the examples, and gloss over the proofs.
There are also many presentations of this work online that may help you understand the material;
 for example here are some [slides from CMU's 15-745 course](http://www.cs.cmu.edu/afs/cs/academic/class/15745-s19/www/lectures/L10-Lazy-Code-Motion.pdf).

There is also a pretty good [Youtube video](https://www.youtube.com/watch?v=zPLbAOdIqRw) 
 that provides some examples. 

Some terminology:
- down-safety: a.k.a. anticipated, needed. An expression is anticipated at point p if it's guaranteed to be computed in all outgoing paths.
- up-safety: available. An expression is available at point p if it's guaranteed to be computed in all incoming paths.
