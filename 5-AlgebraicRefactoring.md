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

1. Every location that sets a "colour" will need to be changed to "color".
1. `updateItem(self, key, value)` accepts keys that are not part of the ToDoItem definition (at least not that used by the constructor).
1. `updateItem(self, key, value)` is of type `(S("dueDate"), Date) | (S("description"), String) | (S("status"), Status) | (S("colour"), Colour) | (S("isPublic"), boolean) -> None`.
   We can split these up into individual setters:
   ```
   def updateDueDate(self, value):
     setattr(self, "dueDate", value)
   
   def updateDescription(self, value):
     setattr(self, "description", value)
   
   def updateStatus(self, value):
     setattr(self, "status", value)
   
   def updateColour(self, value):
     setattr(self, "colour", value)
   
   def updateIsPublic(self, value):
     setattr(self, "isPublic", value)
   ```

### Mechanically Refactoring a Weak API

1. At a minimum, you could extract color depth and screen dimensions for each mode as static values and call them from the new print function. Even better though would be to create a `Mode` type to encapsulate those graphical settings and have small, medium, etc. as static values.
1. Probably only that there are a set of magic keys ("small", "medium") which need to be passed in depending on the machine.
1. 
   ```
   public class Mode {
     int colorDepth;
     Dimension dimension; // (x, y)
   }
   
   final Mode small = new Mode(8, new Dimension(1024, 768));
   final Mode medium = new Mode(16, new Dimension(1600, 1200));
   
   public void displayGame(Mode mode) {
     setColorDepth(mode.colorDepth);
     drawRect(screen, mode.dimension.x, mode.dimension.y);
   }
   ```
1. 
   1. Reverse substitution:
      ```
      public void displayGame(String mode) {
        if (mode.equals("small")) {
          displayGame(8, 1024, 768);
        } else if (mode.equals("medium")) {
          displayGame(16, 1600, 1200);
        }
      }
      
      private void displayGame(int depth, int x, int y) {
        setColorDepth(depth);
        drawRect(screen, x, y);
      }
      ```
   1. Pull out the modes
      ```
      final String small = "small";
      final String medium = "medium";
      
      public void displayGame(String mode) {
        if (mode.equals(small)) {
          displayGame(8, 1024, 768);
        } else if (mode.equals(medium)) {
          displayGame(16, 1600, 1200);
        }
      }

      private void displayGame(int depth, int x, int y) {
        setColorDepth(depth);
        drawRect(screen, x, y);
      }
      ```
   1. Substitute a tuple for the string
      ```
      final Tuple small = (8, 1024, 768);
      final Tuple medium = (8, 1600, 1200);
      
      public void displayGame(Tuple mode) {
        setColorDepth(mode.a);
        drawRect(screen, mode.b, mode.c);
      }
      ```
   1. Finally substitute a record type for the tuple (see answer 3).

## Case Study: A Tale of Two Parsers

### Data Modeling

TODO

### Code follows data

TODO
