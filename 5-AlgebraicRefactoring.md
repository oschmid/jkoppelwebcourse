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
   [true/x](if (x) a else b) && [false/x](if (x) a else b)
   (if (true) a else b) && (if (false) a else b)
   a && b
   (if (false) b else a) && (if (true) b else a)
   (if (!true) b else a) && (if (!false) b else a)
   [true/x](if (!true) b else a) && [false/x](if (!false) b else a)
   if (!x) b else a
   ```
