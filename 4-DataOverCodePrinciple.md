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
   - **Input** hides the expected input format (currently a list of string lists).
   - **Circular Shifter** hides nothing.
   - **Alphabetizing** hides the sort order.
   - **Output** hides where to direct the output (e.g. terminal), and how lines are formatted.
   - **Master Control** hides the example data, and the order each operation is called.
1. 
   1. All modules except Master Control would need to change as they all access `line_storage` directly. Preferably a new storage module would be created to hide how lines are stored (in-memory vs disk vs some future mechanism).
   1. Master Control would need to change to not call `alphabetize()`, Alphabetize would need to change to use selection instead of sorting, and Output would need to change how it communicates with Alphabetize (instead of accessing `alph_index` directly).
   1. Modules accesssing `circ_index` (Circular Shifter and Alphabetizing) would need to change to handle the new format.
1. 
   ```python
   # STORAGE MODULE
   
   line_storage = None
   
   # Returns lines as an array
   def get_lines():
      global line_storage
      return line_storage
   
   def add_lines(lines):
      global line_storage
      line_storage.extend(lines)
   
   
   # CIRCULAR SHIFTER MODULE
   
   circ_index = None
   
   # Returns shifts as a list of (line, shift index) pairs
   def get_circ_index(shift, wordno):
      global circ_index
      return circ_index
   
   def add_circ_shift(lineno, wordno):
      global circ_index
      circ_index.append((lineno, wordno))
   
   
   # ALPHABETIZING MODULE
   
   alph_index = None
   
   # Returns shifts as alphabetized list of (line, shift index) pairs
   def get_alph_index():
      global alph_index
      return alph_index
   
   # Add list of (line, shift index) pairs
   def add_alph(shifts):
      global alph_index
      alph_index.extend(shifts)
      alph_index = sorted(alph_index, cmp=cmp_csline)
   ```
1.
   ```python
   # TODO
   ```
1. 
   - **Storage** hides the format of`line_storage`
   - **Input** hides the expected input format (currently a list of string lists).
   - **Circular Shifter** hides the format of `circ_index`
   - **Alphabetizing** hides the sort order and the format of `alph_index`.
   - **Output** hides where to direct the output (e.g. terminal), and how lines are formatted.
   - **Master Control** hides the example data, and the order each operation is called.
1. 
   1. Only Storage would need to change.
   1. Alphabetizing and how it's accessed by Output would need to change.
   1. Only Circular Shifter would need to change.

## Case Study: Git

TODO

## Optional Design Exercise: Interactive Fiction

https://gist.github.com/oschmid/f77c3650aabcf7bc2d8fb1ec4350e7af
