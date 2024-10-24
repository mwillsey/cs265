# CS 265: Compiler Optimization

Welcome to CS 265, Fall 2024 edition!

- Instructor: [Max Willsey](https://mwillsey.com)
- Time: Tuesdays and Thursdays, 2:00pm-3:30pm 
- Location: Soda 405
- Office hours: After class (TTh 3:30-4:30pm), 725 Soda

This course uses my fork
 of the [bril compiler infrastructure](https://github.com/mwillsey/bril/).


## Other Course Pages

- [bCourses/Canvas](https://bcourses.berkeley.edu/courses/1538171) (enrollment required)
- [Syllabus](./syllabus.md)
- [Project Information](./project.md)
- [Bril](https://github.com/mwillsey/bril/) (Max's fork)

## Schedule 

Schedule is under construction and subject to change.

If the topic says "_OH, no class_", 
 that means I will be available during the normal class time for office hours, 
 in addition to the normal office hours after class.

| Week | Date   | Topic                                                                                             | Due                                                     |
|-----:|--------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------|
|    1 | Aug 29 | [Overview](lessons/00-overview.md)                                                                |                                                         |
|    2 | Sep 3  | [Local Optimizations](lessons/01-local-opt.md)                                                    | Course Survey ([bCourses][])                            |
|      | Sep 5  | [Local Optimizations](lessons/01-local-opt.md)                                                    |                                                         |
|    3 | Sep 10 | [Dataflow](lessons/02-dataflow.md)                                                                |                                                         |
|      | Sep 12 | _no class_                                                                                        |                                                         |
|    4 | Sep 17 | [Dataflow](lessons/02-dataflow.md)                                                                | [Task 1](lessons/01-local-opt.md#task) due              |
|      | Sep 19 | ["Constant Propagation with Conditional Branches"](./reading/sparse-conditional-constant-prop.md) | Reading reflection ([bCourses][])                       |
|    5 | Sep 24 | [SSA](lessons/03-ssa.md)                                                                          |                                                         |
|      | Sep 26 | ["Simple and Efficient Construction of Static Single Assignment Form"](./reading/braun-ssa.md)    | Reading reflection ([bCourses][])                       |
|    6 | Oct 1  | [Loops](lessons/04-loops.md)                                                                      | [Task 2](lessons/02-dataflow.md#task) due               |
|      | Oct 3  | ["Lazy Code Motion"](./reading/lazy-code-motion.md)                                               | Reading reflection ([bCourses][])                       |
|    7 | Oct 8  | [Loops](./lessons/04-loops.md#induction-variables)                                                |                                                         |
|      | Oct 10 | ["Beyond Induction Variables"](./reading/beyond-induction-variables.md)                           | Reading reflection ([bCourses][])                       |
|    8 | Oct 15 | [Memory](./lessons/05-memory.md)                                                                  | [Task 3](lessons/04-loops.md#task) due                  |
|      | Oct 17 | ["Strictly Declarative Specification of Sophisticated Points-to Analyses"](./reading/doop.md)     | Reading reflection ([bCourses][])                       |
|    9 | Oct 22 | [Interprocedural Optimization](./lessons/06-interprocedural.md)                                   |                                                         |
|      | Oct 24 | ["Understanding and exploiting optimal function inlining"](./reading/optimal-inlining.md)         | Reading reflection ([bCourses][])                       |
|   10 | Oct 29 | _OH, no class_                                                                                    | [Task 4](lessons/05-memory.md#task) due                 |
|      | Oct 31 |                                                                                                   |                                                         |
|   11 | Nov 5  | _Election day, no class_                                                                          | [Project Proposals](./project.md#project-proposals)     |
|      | Nov 7  |                                                                                                   |                                                         |
|   12 | Nov 12 |                                                                                                   |                                                         |
|      | Nov 14 |                                                                                                   |                                                         |
|   13 | Nov 19 |                                                                                                   |                                                         |
|      | Nov 21 |                                                                                                   |                                                         |
|   14 | Nov 26 |                                                                                                   | [Project Check-ins due](./project.md#project-check-ins) |
|      | Nov 28 | _Holiday, no class_                                                                               |                                                         |
|   15 | Dec 3  | _no class_                                                                                        |                                                         |
|      | Dec 5  | "                                                                                                 |                                                         |
|   16 | Dec 10 | _RRR, no class_                                                                                   |                                                         |
|      | Dec 12 | "                                                                                                 |                                                         |
|   17 | Dec 17 | _Finals Week_                                                                                     | [Project Reports](./project.md#project-report)          |

## Resources

These notes are by no means meant to be a comprehensive resource for the course.
Here are some other resources
 (all freely available online)
 that you may find helpful.
I have looked at many of these resources in preparing this class,
 and I recommend them for further reading.

- Other courses
  - CMU's
     [15-411](https://www.cs.cmu.edu/~fp/courses/15411-f14/schedule.html) by Frank Pfenning (notes);
     [15-745](http://www.cs.cmu.edu/afs/cs/academic/class/15745-s19/www/syllabus.html) by Phil Gibbons (slides)
  - Cornell's [CS 6120](https://www.cs.cornell.edu/courses/cs6120/) 
    by Adrian Sampson (videos)
  - Stanford's [CS 243](https://suif.stanford.edu/~courses/cs243/)
    by Monica Lam (slides)
- The book _[Static Program Analysis](https://cs.au.dk/~amoeller/spa/)_  by Anders Møller and Michael I. Schwartzbach 
  (available online for free).
- The survey paper "[Compiler transformations for high-performance computing](https://dl.acm.org/doi/10.1145/197405.197406)" (1994)
  for a comprehensive look at many parts of an optimizing compiler, especially loop transformations.

[bCourses]: https://bcourses.berkeley.edu/courses/1538171