## Readings

### On the Criteria to be Used in Decomposing Systems Into Modules

Line Storage Module:
   CHAR(r,w,c)
   SETCHAR(r,w,c,d)
Input Module:
   ?
Circular Shifter Module:
   CSCHAR(l,w,c)
   CSSETUP
Alphabetizer Module:
   ALPH
   ITH(i)
Output Module
Master Control Module

TODO some sort of hidden coupling?

### The Secret History of Information Hiding

TODO probably but can't remember one right now...

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
   def get_circshift(shift, wordno):
      (lno, first_word_no) = shift
      shift_idx = (first_word_no + word_no) % len(lines[lno])
      return get_lines()[lno][shift_idx]
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

### Worktrees

1. `struct worktree` is in **worktree.h**
1. `get_main_worktree(void)` in **worktree.c**
1. `is_bare_repository(void)` in **environment.c** checks if the default repository is bare or if there is a worktree.

### Submodules

1. `struct submodule` is in **submodule-config.h**
1. (see below)
1. `submodule_from_name()` and `submodule_from_path()` in **submodule-config.c**
1. `lookup_or_create_by_name()` in **submodule-config.c**
1. `git_config_from_file()`, along with `git_config_from_file_with_options()`, `git_config_from_mem()`, `git_config_from_blob_oid()`, and `git_config_from_parameters()` in **config.c**
1. `parse_config()` in **submodule-config.c**
1. `repo->submodule_cache` and (judging by [documentation](https://git-scm.com/docs/gitsubmodules)) a file is written to .gitmodules and some submodule info is written to the git repo's config.

### Freestyle

1. How are git stashes modeled?
   - `struct stash_info` contains the necessary git tree and commit objects to reconstruct the worktree and index, even untracked files. A base commit is also stored for merging file changes when popping from stash.
1. How does git status find changed files?
   - **wt-status.c** has several methods that recursively scan files
    - `wt_status_collect_changed`
    - `wt_status_collect_updated`
    - `wt_status_collect_changes_worktree`
    - `wt_status_collect_changes_index`
    - `wt_status_collect_changes_initial`
    - `wt_status_collect_untracked`
1. What is a detached head state?
   - `is_detached` is a flag on `struct worktree` in **worktree.h**. I gave up searching through the code for what this actually meant and looked up some documentation. [This](https://www.git-tower.com/learn/git/faq/detached-head-when-checkout-commit) helped me to understand how it's the state of having checked out a commit directly without being on a specific branch. This kind of question is more abstract and less likely to be answered directly in code.

## Optional Design Exercise: Interactive Fiction

https://gist.github.com/oschmid/88f1e2fafd852c7be4abb4ed1eb1b2cf
