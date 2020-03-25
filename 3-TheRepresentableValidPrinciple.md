## Readings

### Applying the Linus Torvalds "Good Taste" coding requirement

Limit the number of states and state transitions to the minimum number necessary.
(The linked medium article looks like it's being monetized. Medium says I have only 2 free stories left. Some students may find it hard to access it.)

### Bugs and Battleships

For fizzbuzz this would be multiples of only 3, multiples of only 5, multiples of both 3 and 5, multiples of neither. Changes to the spec that increase the state space may split test cases. We could add another case to fizzbuzz (e.g. multiples of 45) that would split the "multiples of both 3 and 5" case into "multiples of 3 and 5 but not 45" and "multiples of 45".

### The Most Dangerous Code in the World

They would need to slowly release updates to their library over time with backwards compatible breaking changes to get users to eventually migrate to a simpler version. Which would likely never happen. They could also release a new version of their library wrapping confusing API such as this will a simpler strictly secure one. With good documentation this would also help demonstrate to others how to properly use the more advanced flags. (Another benefit would be to dogfood their library...)

### Where to Draw the Boundary

TODO

### Optional: Evolving an Embedded Domain-Specific Language in Java

TODO

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
      Either<InProgressGame, NewGame> takeMoveBack()
      boolean isPositionOccupied(Position)
    }

    class FinishedGame {
      Either<InProgressGame, NewGame> takeMoveBack()
      boolean isPositionOccupied(Position)
      Result whoWonOrDraw()
    }

    class PlayerWon implements Result {
      Player player;
    }

    class Draw implements Result {
    }
    
    class TopLeft implements Position {}
    class TopMiddle implements Position {}
    class TopRight implements Position {}
    class MiddleLeft implements Position {}
    // ...
    class BottomRight implements Position {}
    ```
1. 
    ```
    class NewGame:
      def move(self, player, position):
        // return either InProgressGame or FinishedGame
    
    class InProgressGame:
      def move(self, player, position):
        // return either InProgressGame or FinishedGame
      def takeMoveBack(self):
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
    
    static Either<InProgressGame<Occupied, B, C, D, E, F, G, H, I>, FinishedGame moveA(Player, NewGame)
    static Either<InProgressGame<A, Occupied, C, D, E, F, G, H, I>, FinishedGame moveA(Player, NewGame)
    // repeat for moveC, moveD, ...
    
    static Either<InProgressGame<Occupied, B, C, D, E, F, G, H, I>, FinishedGame> moveA(Player,
        InProgressGame<Unoccupied, B, C, D, E, F, G, H, I>)
    // repeat for moveB, moveC, ...
    
    static Either<InProgressGame<Unoccupied, B, C, D, E, F, G, H, I>, NewGame> moveA(Player,
        InProgressGame<Occupied, B, C, D, E, F, G, H, I>)
    // repeat for moveB, moveC, ...
    
    static Either<InProgressGame<Unoccupied, B, C, D, E, F, G, H, I>, NewGame> moveA(Player, FinishedGame)
    // repeat for moveB, moveC, ...
    ```