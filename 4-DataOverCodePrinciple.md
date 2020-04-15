## Readings

### On the Criteria to e Used in Decomposing Systems Into Modules

TODO some sort of hidden coupling?

### The Secret History of Information Hiding

TODO

## KWIC Refactoring Drill

### Dataflow Patterns

1. In the alphabetize module, the function `def csword(shift, wordno, lines)` and in the output module, inside the function `def csline(shift, lines)`.
1. ![Dataflow](https://github.com/oschmid/jkoppelwebcourse/raw/master/4-DataOverCode-Dataflow.png)
1. 2-D array access involving an addition and a modulus.

### Data-Centric Refactoring

1. Input = TODO
   Circular Shifter = TODO
   Alphabetizing = TODO
   Output = TODO
   Master Control = TODO
1. 
  1. All modules except Master Control would need to change as they all access `line_storage` directly. Preferably a new storage module would be created to hide how lines are stored (in-memory vs disk vs some future mechanism).
  1. TODO
  1. TODO
1. TODO
1. TODO
1. TODO
1. TODO

## Case Study: Git

TODO

## Optional Design Exercise: Interactive Fiction

https://gist.github.com/oschmid/f77c3650aabcf7bc2d8fb1ec4350e7af
