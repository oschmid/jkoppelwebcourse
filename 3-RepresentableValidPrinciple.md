## Readings

### Applying the Linus Torvalds "Good Taste" coding requirement

Limit the number of states and state transitions to the minimum number necessary.
(The linked medium article looks like it's being monetized. Medium says I have only 2 free stories left. Some students may find it hard to access it.)

### Bugs and Battleships

For fizzbuzz this would be multiples of only 3, multiples of only 5, multiples of both 3 and 5, multiples of neither. Changes to the spec that increase the state space may split test cases. We could add another case to fizzbuzz (e.g. multiples of 45) that would split the "multiples of both 3 and 5" case into "multiples of 3 and 5 but not 45" and "multiples of 45".

### The Most Dangerous Code in the World

They would need to slowly release updates to their library over time with backwards compatible breaking changes to get users to eventually migrate to a simpler version. Which would likely never happen. They could also release a new version of their library wrapping confusing API such as this will a simpler strictly secure one. With good documentation this would also help demonstrate to others how to properly use the more advanced flags. (Another benefit would be to dogfood their library...)

### Where to Draw the Boundary

Well-designed data structures encapsulate true units of the program's reality and are easily evolved. Poorly-designed ones repeatedly hold back development and confuse other developers.

## Restricting APIs

### Exercise 1

1. Negative time, temperature that's too low (e.g. lower than freezing), temperature that's too high (e.g. higher than boiling), and mode that isn't LOW/MEDIUM/HIGH. Instead of ints we can use 3 domain specific types Duration, Temperature, Mode that enforce validity on construction.
1. An interface that only exposes `isOn()`.
1. An `isOn(int index)` that returns true iff there's a `WashingMachine` at that index and it's on. (Returns false if index is out of bounds.)

### Exercise 2

1. 
    ```
    class NewGame {
      Either<InProgressGame, FinishedGame> move(Player, Position)
    }

    class InProgressGame {
      Either<InProgressGame, FinishedGame> move(Player, Position)
      Either<InProgressGame, NewGame> takeMoveBack(Position)
      boolean isPositionOccupied(Position)
    }

    class FinishedGame {
      Either<InProgressGame, NewGame> takeMoveBack(Position)
      boolean isPositionOccupied(Position)
      Result whoWonOrDraw()
    }

    class PlayerWon implements Result {
      Player player;
    }

    class Draw implements Result {
    }
    
    enum Position {
        TopLeft, TopMiddle, TopRight,
        MiddleLeft, MiddleMiddle, MiddleRight,
        BottomLeft, BottomMiddle, BottomRight
    }
    ```
1. 
    ```
    class NewGame:
      def move(self, player, position):
        // return either InProgressGame or FinishedGame
    
    class InProgressGame:
      def move(self, player, position):
        // return either InProgressGame or FinishedGame
      def takeMoveBack(self, position):
        // return either NewGame or InProgressGame
      def isPositionOccupied(self, position):
        // return boolean
    
    class FinishedGame:
      def takeMoveBack(self):
        // return either NewGame or InProgressGame
      def isPositionOccupied(self, position):
        // return boolean
      def whoWonOrDraw(self):
        // return PlayerWon or Draw
    
    class PlayerWon:
      player
    
    class Draw:
      pass
    ```
1. 
    ```
    // we could get most of the safety by having 'isPositionOccupied()' return a token needed 
    // to for move() and 'takeMoveBack()'. The token would be of type Either<Occupied, Unoccupied> 
    // and move() would accept Unoccupied and takeMoveBack() would accept Occupied. Both token 
    // types have constructors private to the *Game types.
    // 
    // But here's a way of getting true compile time guarrantee of only making valid moves
    
    class NewGame {
    }
    
    class InProgressGame<A, B, C, D, E, F, G, H, I> { // where each extends IOccupied
        boolean isPositionOccupied(Position)
    }
    
    class Occupied implements IOccupied {}
    
    class Unoccupied implements IOccupied {}
    
    class FinishedGame {
        boolean isPositionOccupied(Position)
        Result whoWonOrDraw()
    }

    class PlayerWon implements Result {
      Player player;
    }

    class Draw implements Result {
    }
    
    interface Position {}
    
    class TopLeft implements Position {}
    class TopMiddle implements Position {}
    class TopRight implements Position {}
    class MiddleLeft implements Position {}
    // ...
    class BottomRight implements Position {}
    
    static Either<InProgressGame<Occupied, B, C, D, E, F, G, H, I>, FinishedGame move(Player, TopLeft, NewGame)
    static Either<InProgressGame<A, Occupied, C, D, E, F, G, H, I>, FinishedGame move(Player, TopMiddle, NewGame)
    // repeat for TopRight, MiddleLeft, ...
    
    static Either<InProgressGame<Occupied, B, C, D, E, F, G, H, I>, FinishedGame> move(Player, TopLeft,
        InProgressGame<Unoccupied, B, C, D, E, F, G, H, I>)
    // repeat for TopMiddle, TopRight, ...
    
    static Either<InProgressGame<Unoccupied, B, C, D, E, F, G, H, I>, NewGame> takeMoveBack(TopLeft,
        InProgressGame<Occupied, B, C, D, E, F, G, H, I>)
    // repeat for TopMiddle, TopRight, ...
    
    static Either<InProgressGame<Unoccupied, Occupied, Occupied, Occupied, Occupied, Occupied, Occupied, Occupied, Occupied>, NewGame> takeMoveBack(TopLeft, FinishedGame)
    // repeat for moveB, moveC, ...
    ```
## Simpler and More Correct

### Exercise 1

1.
    ```
    public class Discount {
        public static final String STUDENT = "student";
        public static final String EMPLOYEE = "employee";
        // ...
        public boolean doesDiscountApply(Customer c, Item item) {
            if (customerTypeDiscount != null) {
                if (customerTypeDiscount.equals(STUDENT)) return c.isStudent();
                else if (customerTypeDiscount.equals(EMPLOYEE)) return c.isEmployee();
            }
            // ...
        }
    }
    ```
1. 
    ```
    public class Discount {
        private @NonNull CustomerTypeDiscount customerTypeDiscount;
        // ...
        public boolean doesDiscountApply(Customer c, Item item) {
            switch (customerTypeDiscount) {
                case STUDENT: return c.isStudent();
                case EMPLOYEE: return c.isEmployee();
                case NONE: break;
            }
            // ...
        }
    }
    
    public enum CustomerType {
        STUDENT, EMPLOYEE, NONE
    }
    ```
1. 
    ```
    public class Discount {
        // ...
        
        private boolean doesDiscountApply(Customer c, Item item) {
            // ...
        }

        public double applyDiscount(Customer c, Item item, double price) {
            if (!doesDiscountApply(c, item)) return price;
            return price * (1 - discountPercent);
        }
    }
    ```
1.
    ```
    public abstract Discount {
        // ...
        abstract boolean doesDiscountApply(Customer c, Item item);
        // ...
    }
    
    public class StudentDiscount extends Discount {
        private final double discountPercent;
        
        boolean doesDiscountApply(Customer c, Item item) {
            return c.isStudent();
        }
    }
    
    public class EmployeeDiscount extends Discount {
        private final double discountPercent;
        
        boolean doesDiscountApply(Customer c, Item item) {
            return c.isEmployee();
        }
    }
    
    public class ItemDiscount extends Discount {
        private final String itemName;
        private final double discountPercent;
        
        boolean doesDiscountApply(Customer c, Item item) {
            return item.getName().equals(itemName);
        }
    }
    
    public class DayOfWeekDiscount extends Discount {
        private final String dayOfWeek;
        private final double discountPercent;
        
        boolean doesDiscountApply(Customer c, Item item) {
            return DateUtils.getDayOfWeek().equals(dayOfWeek);
        }
    }
    ```
1. 
    ```
    public abstract Discount {
        // ...
        abstract boolean doesDiscountApply(Customer c, Item item, OffsetDateTime dateTime);
        // ...
    }
    
    public class DayOfWeekDiscount extends Discount {
        private final DayOfWeek dayOfWeek;
        private final double discountPercent;
        
        boolean doesDiscountApply(Customer c, Item item, OffsetDateTime dateTime) {
            return dateTime.getDayOfWeek().equals(dayOfWeek);
        }
    }
    ```
### Exercise 2

Do the checks in the data layer. Instead of `user.getPhotos(db)` and then checking canView, change the API to be `user.getPhotos(viewingUser, db)`.
