# Doop: Strictly Declarative Specification of Sophisticated Points-to Analyses

- [ACM DL PDF](https://dl.acm.org/doi/pdf/10.1145/1639949.1640108)
- [Youtube talk](https://www.youtube.com/watch?v=FQRLB2xJC50)

This paper introduces the Doop framework for specifying points-to analyses in Datalog programs. 
Datalog is a declarative logic programming language (and also a database query language) 
 that is well-suited for expressing pointer analyses.

The paper provides a pretty good introduction to Datalog if you're unfamiliar with it.
For more of a Datalog primer, you can see [these lecture notes from my previous course](https://inst.eecs.berkeley.edu/~cs294-260/sp24/2024-02-05-datalog).

Skip section 4 if you aren't already familiar with Datalog; 
 it described the optimizations needed to make Datalog efficient enough for this purpose.
It's quite interesting, but not necessary to understand from a points-to analysis perspective.

