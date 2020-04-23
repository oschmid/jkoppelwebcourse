## Readings

### On the Criteria to be Used in Decomposing Systems Into Modules

TODO some sort of hidden coupling?

### The Secret History of Information Hiding

TODO

## KWIC Refactoring Drill

### Dataflow Patterns

1. In the alphabetize module, the function `def csword(shift, wordno, lines)` and in the output module, inside the function `def csline(shift, lines)`.
1. ![Dataflow](https://github.com/oschmid/jkoppelwebcourse/raw/master/4-DataOverCode-Dataflow.png)
1. 2-D array access involving an addition and a modulus.

### Data-Centric Refactoring

1.
   - **Input** hides how to input data. Right now it's just a copy of a list of string lists but it could be expanded to reading from a terminal or other data source.
   - **Circular Shifter** hides how and when lines are converted into circular shifts and how those circular shifts are stored.
   - **Alphabetizing** hides how and when circular shifts are sorted, and the sort order.
   - **Output** hides where to direct the output (e.g. terminal), and how lines are formatted.
   - **Master Control** hides what data to load, and the order each operation is called.
1. 
   1. All modules except Master Control would need to change as they all access `line_storage` directly. Preferably a new storage module would be created to hide how lines are stored (in-memory vs disk vs some future mechanism).
   1. Master Control would need to change to not call `alphabetize()`, Alphabetize would need to change to use selection instead of sorting, and Output would need to change how it communicates with Alphabetize (instead of accessing `alph_index` directly).
   1. Modules accesssing `circ_index` (Circular Shifter and Alphabetizing) would need to change to handle the new format.
1. TODO
1. TODO
1. TODO
1. TODO

## Case Study: Git

TODO

## Optional Design Exercise: Interactive Fiction

https://gist.github.com/oschmid/f77c3650aabcf7bc2d8fb1ec4350e7af
