# Constant Propagation with Conditional Branches

- Mark N. Wegman and F. Kenneth Zadeck
- Transactions on Programming Languages and Systems (TOPLAS), 1991 
- [PDF](https://dl.acm.org/doi/pdf/10.1145/103135.103136) from ACM Digital Library

Read Sections 1-5.

## SSA Primer

This paper mentions the use of Static Single Assignment (SSA) form,
 which we will cover in more detail later in the course.
The paper actually provides a pretty accessible introduction to SSA form,
 but you may find it helpful to read more about it
 on the [Wikipedia page](https://en.wikipedia.org/wiki/Static_single-assignment_form)
 or any of the many online resources on this topic.

In short,
 you've probably notices how annoying it is to deal with redefinitions in your value numbering implementation.
SSA form is a way to make this easier by ensuring that each variable is only assigned once.
In simple code,
 this can be done with a simple renaming:
```
x = y;
x = x + 1;
use(x)
```
becomes:
```
x1 = y;
x2 = x1 + 1;
use(x2)
```

What happens when a block has multiple predecessors?
`if`s are a common example of this:
```c
x = 1;
if (cond) x = x + 1;
use(x);
```

SSA's solution is to use the φ (phi) function
 to allow a special definition at the join point of the control flow
 that magically selects the correct value from the predecessors.
So here's the before IR:
```
entry:
  x = 1
  br cond .then .end
.then:
  x = x + 1
.end
  use x
```

And here it is in SSA form:
```
entry:
  x1 = 1
  br cond .then .end
.then:
  x2 = x1 + 1
.end
  x3 = φ(x1, x2)
  use x3
```

Sometimes (and in Bril, as we'll see later),
 the φ function also mentions the labels that define the values:
```
  x3 = φ(entry: x1, .then: x2)
```

This very brief introduction to SSA form should be enough to get you through the paper!



