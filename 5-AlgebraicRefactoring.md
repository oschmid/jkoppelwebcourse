## Readings

### The Algebra of Algebraic Datatypes, Part 1 - Chris Taylor

1.
  ```
  (int + string) -> bool
  Using a product rule we get 2 functions:
    int -> bool
    string -> bool
  ```

### Optional: Program Derivation for Functional Programs - Felienne Hermans

Insertion Sort is a special case of Quick Sort where you focus on inserting one element into a sorted list rather than merging 2 sorted lists.

## Equational Reasoning Drill

1. Changing a function call
   ```
   ((x, y) -> x + y + 1)(a + 1, b)
   ((y) -> [a+1/x](x + y + 1))(b)
   ((y) -> (a + 1 + y + 1))(b)
   () -> [b/y](a + 1 + y + 1)
   () -> (a + 1 + b + 1)
   () -> [a/x](x + 1 + b + 1)
   ((x) -> (x + 1 + b + 1))(a)
   ((x) -> [b+1/y](x + 1 + y))(a)
   ((x, y) -> x + y + 1)(a, b + 1)
   ```
1. Swapping an if-statement
   ```
   if (x) a else b
   [true/x](if (x) a else b), [false/x](if (x) a else b)
   (if (true) a else b), (if (false) a else b)
   a && b
   (if (false) b else a), (if (true) b else a)
   (if (!true) b else a), (if (!false) b else a)
   [true/x](if (!true) b else a), [false/x](if (!false) b else a)
   if (!x) b else a
   ```
1. Un-nesting an if statement
   ```
   if (x) { if (y) a else b } else b
   [true/x](if (x) { if (y) a else b } else b), [false/x](if (x) { if (y) a else b } else b)
   (if (true) { if (y) a else b } else b), (if (false) { if (y) a else b } else b)
   (if (y) a else b), (b)
   [true/y](if (y) a else b), [false/y](if (y) a else b), b
   (if (true) a else b), (if (false) a else b), b
   a, b, b
   [true/x, true/y](a), [true/x, false/y](b), [false/x](b)
   [true/x, true/y](a), [true/x, false/y](b), [false/x, true/y](b), [false/x, false/y](b)
   if (x && y) a else b
   ```
   ```
   if (x) a else { if (y) a else b }
   [true/x](if (x) a else { if (y) a else b }), [false/x](if (x) a else { if (y) a else b })
   if (true) a else { if (y) a else b }, if (false) a else {if (y) a else b}
   a, if (y) a else b
   a, [true/y](if (y) a else b), [false/y](if (y) a else b)
   a, a, b
   [true/x](a), [false/x, true/y](a), [false/x, false/y](b)
   [true/x, true/y](a), [true/x, false/y](a), [false/x, true/y](a), [false/x, false/y](b)
   if (x || y) a else b
   ```
1. Conditional-to-function
   ```
   if (A) o.foo() else o.bar()
   [true/A](if (A) o.foo() else o.bar()), [false/A](if (A) o.foo() else o.bar())
   if (true) o.foo() else o.bar(), if (false) o.foo() else o.bar()
   o.foo(), o.bar()
   f = (() -> o.foo()); f(), f = (() -> o.bar()); f()
   [true/A](f = if (true) (() -> o.foo()) else (() -> o.bar())); f(), [false/A](f = if (false) (() -> o.foo()) else (() -> o.bar())); f()
   f = if (A) (() -> o.foo()) else (() -> o.bar()); f()
   ```
1. Optional Bonus: Functoriality of Map
   ```
   map(f, map(g, l))
   TODO
   
   map((x) -> f(g(x)), l)
   ```

## Mechanical Refactoring Drill

### Algebraically Refactoring a Weak API

1. TODO
1. TODO
1. TODO

### Mechanically Refactoring a Weak API

1. TODO
1. TODO
1. TODO
1. TODO
