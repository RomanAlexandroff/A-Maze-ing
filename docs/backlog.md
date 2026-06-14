

# A-Maze-ing Project Backlog

This file is a simple replacement of a project management tool like Jira, that would be an overkill in such a small project.
This is the place where we take our tasks from. We work on them, we finish them and we come back here to get a new task.

<br>

## Global Definition of Done

Every task is considered finished only when:

* `flake8 .` passes.
* `mypy . --warn-return-any --warn-unused-ignores --ignore-missing-imports --disallow-untyped-defs --check-untyped-defs` passes.
* Relevant tests pass.
* Public functions/classes have type hints and docstrings.
* No unhandled exceptions reach the user during normal invalid-input scenarios.
* Code is merged through a Pull Request into `dev`.
* The developer who wrote the code can explain it during evaluation.

<br>

## List of Development Phases in Recommended Execution Order

```text
001–004     Phase 0:  Project foundation
005–007     Phase 1:  Core types and errors
008–011     Phase 2:  Config parser and validator
012–018     Phase 3:  Reusable generator basics
019–020     Phase 4:  42 pattern
021–024     Phase 5:  Maze validation
025–026     Phase 6:  Solver
027–029     Phase 7:  Output writer
030–033     Phase 8:  Visual representation
034–035     Phase 9:  Full integration
036–040     Phase 10: Tests
041–042     Phase 11: Reusable package build
043–045     Phase 12: Documentation
046–048     Phase 13: Final evaluation hardening
049–050     Phase 14: Bonuses — only if time remains
```


<br>

# Phase 0 — Project Foundation

## Task 001: Confirm architecture and team workflow
- [ ] Taken
- [ ] Done

**Goal:**
Agree on the project architecture before writing feature code.

**Description:**
Create a short project planning document describing the main components:
```text
CLI entry point
Config parser
Config validator
Maze data model
Maze generator
Maze validator
Maze solver
Output writer
Visual renderer
Reusable mazegen package
Tests
```

**Acceptance criteria:**
* `docs/architecture.md` exists.
* It explains the responsibility of each module.
* It explains the intended data flow:
```text
config file → config object → maze generator → solver → output file → renderer
```
* It defines the branch workflow: `feature/* → dev → main`.
* It defines that all code must pass Flake8 and MyPy before merge.

---

## Task 002: Create minimal executable entry point
- [ ] Taken
- [ ] Done

**Goal:**
Prepare the required root-level executable file.

**Description:**
Create the mandatory root file:
```text
a_maze_ing.py
```
The subject requires the program to be runnable as:
```bash
python3 a_maze_ing.py config.txt
```

**Acceptance criteria:**
* `a_maze_ing.py` exists at repository root.
* Running it with no arguments prints a clean usage error.
* Running it with too many arguments prints a clean usage error.
* The file delegates real work to internal application code instead of containing all project logic.
* No traceback is shown for normal user mistakes.

---

## Task 003: Implement mandatory Makefile rules
- [ ] Taken
- [ ] Done

**Goal:**
Satisfy the Makefile requirements from the subject.

**Description:**
Create or update `Makefile` with these mandatory rules:
```make
install
run
debug
clean
lint
```
The subject also recommends optional `lint-strict`.  

**Acceptance criteria:**
* `make install` installs dependencies.
* `make run` runs `python3 a_maze_ing.py config.txt`.
* `make debug` runs the project through `pdb`.
* `make clean` removes Python caches and tool caches.
* `make lint` runs exactly the required Flake8 and MyPy checks.
* `make lint-strict` exists if the team chooses to add it.
* All Makefile commands are documented in the README.

---

## Task 004: Prepare default configuration file
- [ ] Taken
- [ ] Done

**Goal:**
Provide the default config required by the subject.

**Description:**
Create a root-level or `examples/` configuration file with all mandatory keys:
```text
WIDTH=20
HEIGHT=15
ENTRY=0,0
EXIT=19,14
OUTPUT_FILE=maze.txt
PERFECT=True
SEED=42
```
The subject requires a default configuration file in the repository and allows additional keys such as seed, algorithm, or display mode. 

**Acceptance criteria:**
* A valid default config exists.
* It includes all mandatory keys.
* It includes a seed for reproducible test runs.
* `make run` works with this config.
* Invalid example configs are later added for testing.

---

<br>

# Phase 1 — Core Types and Data Model

## Task 005: Define wall bit constants
- [ ] Taken
- [ ] Done

**Goal:**
Create one authoritative representation of maze walls.

**Description:**
Implement constants or an enum for wall bits:
```text
North = 1
East  = 2
South = 4
West  = 8
```
The output format requires one hexadecimal digit per cell, where bits represent closed walls. 

**Acceptance criteria:**
* Wall bits are defined in one place only.
* Code never uses unexplained magic numbers like `1`, `2`, `4`, `8` directly.
* Helper functions exist for checking, adding, and removing walls.
* Unit tests verify the wall-bit mapping.

---

## Task 006: Define coordinate and maze types
- [ ] Taken
- [ ] Done

**Goal:**
Make the codebase type-safe and easy to reason about.

**Description:**
Create core type aliases or dataclasses for:
```text
Coordinate
MazeGrid
MazePath
Direction
```
Example:
```python
Coordinate = tuple[int, int]
MazeGrid = list[list[int]]
```

**Acceptance criteria:**
* Shared types are defined in one module.
* Generator, solver, renderer, and output writer use the same types.
* MyPy passes with no untyped function definitions.
* Types are documented.

---

## Task 007: Create project-specific exceptions
- [ ] Taken
- [ ] Done

**Goal:**
Handle errors gracefully without random crashes.

**Description:**
Create custom exceptions such as:
```text
ConfigError
MazeGenerationError
MazeValidationError
OutputError
RenderError
```
The subject states that invalid configuration, file not found, bad syntax, impossible parameters, and similar cases must be handled gracefully with clear messages. 

**Acceptance criteria:**
* Normal user errors are converted into readable messages.
* No raw traceback appears for expected invalid input.
* CLI catches project exceptions at the top level.
* Tests cover at least several error cases.

---

<br>

# Phase 2 — Configuration Parser and Validator

## Task 008: Implement raw config file loader
- [ ] Taken
- [ ] Done

**Goal:**
Read config files safely.

**Description:**
Implement a loader that opens the config file using a context manager and returns raw lines.

**Acceptance criteria:**
* File reading uses `with open(...)`.
* Missing file produces a clean error message.
* Empty file produces a clean validation error.
* File access errors do not crash the program.
* Unit tests cover missing and empty files.

---

## Task 009: Implement `KEY=VALUE` parser
- [ ] Taken
- [ ] Done

**Goal:**
Parse the subject-defined config format.

**Description:**
The config file must contain one `KEY=VALUE` pair per line. Lines starting with `#` are comments and must be ignored. Mandatory keys are `WIDTH`, `HEIGHT`, `ENTRY`, `EXIT`, `OUTPUT_FILE`, and `PERFECT`. 

**Acceptance criteria:**
* Blank lines are ignored.
* Comment lines are ignored.
* `KEY=VALUE` lines are parsed correctly.
* Malformed lines produce a clear error.
* Duplicate keys are either rejected or handled by a clearly documented rule.
* Tests cover comments, blank lines, malformed lines, and valid lines.

---

## Task 010: Implement config value conversion
- [ ] Taken
- [ ] Done

**Goal:**
Convert raw config strings into typed Python values.

**Description:**
Convert:
```text
WIDTH        → int
HEIGHT       → int
ENTRY        → tuple[int, int]
EXIT         → tuple[int, int]
OUTPUT_FILE  → str
PERFECT      → bool
SEED         → optional str/int
```

**Acceptance criteria:**
* Boolean parsing accepts only clearly documented values, for example `True` and `False`.
* Coordinates must use `x,y` format.
* Invalid integers, booleans, and coordinates produce clean errors.
* Tests cover every config field.

---

## Task 011: Implement semantic config validation
- [ ] Taken
- [ ] Done

**Goal:**
Reject impossible maze parameters before generation starts.

**Description:**
Validate:
```text
width > 0
height > 0
entry inside bounds
exit inside bounds
entry != exit
output file name is not empty
```
The subject requires entry and exit to exist, be different, and be inside maze bounds. 

**Acceptance criteria:**
* Invalid dimensions are rejected.
* Entry outside bounds is rejected.
* Exit outside bounds is rejected.
* Same entry/exit is rejected.
* Meaningful error messages are shown to the user.
* Tests cover all invalid cases.

---

<br>

# Phase 3 — Reusable Maze Generator Package

## Task 012: Design public `MazeGenerator` API
- [ ] Taken
- [ ] Done

**Goal:**
Create the reusable generator class required by the subject.

**Description:**
The subject requires maze generation to be implemented as a unique class, such as `MazeGenerator`, inside a standalone module that can be imported in a future project. It must allow users to instantiate the generator, pass custom parameters, access the maze structure, and access at least one solution. 

**Acceptance criteria:**
* A `MazeGenerator` class exists.
* It can be imported independently from the CLI.
* It accepts width, height, entry, exit, perfect mode, and seed.
* It exposes generated maze data.
* It exposes a solution path.
* It does not depend on CLI, renderer, or output-file code.
* Public methods have Google-style or NumPy-style docstrings.

---

## Task 013: Initialize a fully-walled maze grid
- [ ] Taken
- [ ] Done

**Goal:**
Create the starting maze matrix.

**Description:**
Generate a `height × width` matrix where every cell initially has all four walls closed.

**Acceptance criteria:**
* Every cell starts with North, East, South, and West walls.
* Matrix dimensions match config.
* No row aliasing bug exists.
* Unit tests verify several sizes, including `1x1`, `2x2`, and rectangular mazes.

---

## Task 014: Implement wall coherence helpers
- [ ] Taken
- [ ] Done

**Goal:**
Guarantee that neighboring cells agree about shared walls.

**Description:**
Create helper functions such as:
```text
remove_wall_between(cell_a, cell_b)
has_wall(cell, direction)
get_neighbors(cell)
```
The subject explicitly forbids incoherent walls, such as one cell having an east wall while its eastern neighbor has no west wall. 

**Acceptance criteria:**
* Opening a passage updates both cells.
* Invalid non-neighbor wall changes are rejected.
* Tests verify north/south and east/west coherence.
* Generator code uses these helpers instead of editing raw bits everywhere.

---

## Task 015: Implement deterministic random seed handling
- [ ] Taken
- [ ] Done

**Goal:**
Make maze generation reproducible.

**Description:**
The subject requires random generation but also reproducibility via a seed. 

**Acceptance criteria:**
* Same config and same seed produce the same maze.
* Different seeds usually produce different mazes.
* Missing seed produces random behavior, if you choose to support missing seed.
* The seed used is stored or accessible for debugging.
* Tests verify reproducibility.

---

## Task 016: Implement perfect maze generation
- [ ] Taken
- [ ] Done

**Goal:**
Generate a valid perfect maze.

**Description:**
Implement a maze generation algorithm that creates exactly one path between cells in perfect mode. Recommended first algorithm: iterative randomized depth-first search / recursive backtracker.
The subject says that when `PERFECT=True`, the maze must contain exactly one path between entry and exit. 

**Acceptance criteria:**
* Perfect mode creates a connected maze.
* No loops are introduced in perfect mode.
* There is exactly one valid path between entry and exit.
* The algorithm avoids Python recursion-depth problems.
* Tests verify that perfect mazes are connected and acyclic.

---

## Task 017: Implement non-perfect maze mode
- [ ] Taken
- [ ] Done

**Goal:**
Support `PERFECT=False`.

**Description:**
After generating a valid base maze, optionally remove additional walls to create loops while preserving validity.

**Acceptance criteria:**
* `PERFECT=False` still produces a connected maze.
* Entry and exit remain reachable.
* Wall coherence remains valid.
* External border walls remain closed.
* Additional openings do not create forbidden large open areas.
* Tests verify that non-perfect mode differs from perfect mode for the same seed, if possible.

---

## Task 018: Implement external border protection
- [ ] Taken
- [ ] Done

**Goal:**
Ensure outside borders remain closed.

**Description:**
The subject states that because entry and exit are specific cells, external borders must have walls. 

**Acceptance criteria:**
* Top row always has north walls.
* Bottom row always has south walls.
* Left column always has west walls.
* Right column always has east walls.
* Entry and exit do not create holes in the outer border.
* Tests verify borders on several maze sizes.

---

<br>

# Phase 4 — “42” Pattern

## Task 019: Define the “42” pattern representation
- [ ] Taken
- [ ] Done

**Goal:**
Create a clean internal representation for fully closed “42” cells.

**Description:**
Represent the visible “42” pattern as a list of coordinates or a small mask. Fully closed pattern cells should remain closed and visually distinguishable.
The subject requires the visual maze to contain a visible “42” drawn by several fully closed cells. If the maze is too small, the pattern may be omitted, but an error message must be printed. 

**Acceptance criteria:**
* Pattern data is stored separately from generator logic.
* Pattern cells are identifiable by the renderer.
* Pattern cells remain fully closed.
* Pattern code is documented.

---

## Task 020: Place the “42” pattern into the maze
- [ ] Taken
- [ ] Done

**Goal:**
Embed the pattern without breaking the maze.

**Description:**
Place the pattern in a reasonable location, preferably centered. Pattern cells should become blocked/closed cells and should be excluded from normal maze carving.

**Acceptance criteria:**
* Pattern is visible for sufficiently large mazes.
* Pattern placement does not overwrite entry or exit.
* Pattern placement does not create unreachable non-pattern cells.
* Too-small mazes produce a clear warning or error message.
* Tests cover large enough and too-small mazes.

---

<br>

# Phase 5 — Maze Validation

## Task 021: Implement wall-coherence validator
- [ ] Taken
- [ ] Done

**Goal:**
Validate the generated maze before exporting.

**Description:**
Create a validator that scans every neighboring cell pair and confirms shared wall consistency.

**Acceptance criteria:**
* East/west mismatches are detected.
* North/south mismatches are detected.
* Valid mazes pass.
* Invalid manually-created mazes fail.
* Tests cover both valid and invalid examples.

---

## Task 022: Implement connectivity validator
- [ ] Taken
- [ ] Done

**Goal:**
Guarantee there are no isolated cells except valid pattern cells.

**Description:**
Use BFS or DFS to verify that all normal maze cells are reachable from the entry.
The subject requires full connectivity and no isolated cells, except the “42” pattern. 

**Acceptance criteria:**
* All non-pattern cells are reachable.
* Pattern cells may be excluded from reachability checks.
* Entry and exit must be reachable.
* Tests cover disconnected mazes.

---

## Task 023: Implement large-open-area validator
- [ ] Taken
- [ ] Done

**Goal:**
Prevent forbidden open areas.

**Description:**
The subject says corridors cannot be wider than 2 cells. A `2x3` or `3x2` open area is allowed, but a `3x3` open area is forbidden. 

**Acceptance criteria:**
* Validator detects forbidden `3x3` open areas.
* Validator allows normal corridors.
* Validator allows permitted `2x3` or `3x2` cases.
* Generation either prevents or repairs invalid open areas.
* Tests cover both allowed and forbidden patterns.

---

## Task 024: Implement final maze validation pipeline
- [ ] Taken
- [ ] Done

**Goal:**
Run all validators in one place.

**Description:**
Create one function or class that validates:
```text
entry/exit validity
wall coherence
external borders
connectivity
pattern correctness
large open areas
perfect-mode path uniqueness
```

**Acceptance criteria:**
* Generator output is validated before export.
* Validation errors are readable.
* CLI shows validation failures cleanly.
* Tests cover the validation pipeline.

---

<br>

# Phase 6 — Maze Solver

## Task 025: Implement BFS shortest-path solver
- [ ] Taken
- [ ] Done

**Goal:**
Find the shortest valid path from entry to exit.

**Description:**
Implement BFS over the generated maze. The solver must respect walls and return both coordinates and movement letters.
The output file must include the shortest valid path from entry to exit using `N`, `E`, `S`, and `W`. 

**Acceptance criteria:**
* Solver returns a list of coordinates.
* Solver returns a list of direction letters.
* Solver respects closed walls.
* Solver returns the shortest path.
* Solver handles unsolvable mazes gracefully.
* Tests verify simple known mazes.

---

## Task 026: Implement path uniqueness check for perfect mode
- [ ] Taken
- [ ] Done

**Goal:**
Prove that perfect mode satisfies the subject.

**Description:**
Create a validation helper that confirms there is exactly one path between entry and exit when `PERFECT=True`.

**Acceptance criteria:**
* Perfect mazes pass uniqueness validation.
* A maze with loops fails uniqueness validation.
* The check is used in tests.
* The check is not unnecessarily expensive for normal use, or is only used in validation/test mode.

---

<br>

# Phase 7 — Output File Writer

## Task 027: Implement hexadecimal maze export
- [ ] Taken
- [ ] Done

**Goal:**
Write the maze matrix in the required file format.

**Description:**
Each cell must be written as one hexadecimal digit. Cells are stored row by row, one row per line. 

**Acceptance criteria:**
* Output contains exactly `HEIGHT` maze rows.
* Each maze row contains exactly `WIDTH` hex digits.
* Hex digits correctly represent wall bits.
* All output lines end with `\n`.
* File writing uses a context manager.
* Tests verify exact output for a small known maze.

---

## Task 028: Export entry, exit, and solution path
- [ ] Taken
- [ ] Done

**Goal:**
Complete the required output file format.

**Description:**
After the maze rows, write:
```text
empty line
entry coordinates
exit coordinates
solution path letters
```
The subject requires these three elements after an empty line. 

**Acceptance criteria:**
* Output contains one empty line after the maze.
* Entry is written as `x,y`.
* Exit is written as `x,y`.
* Path is written as a single string of `N`, `E`, `S`, `W`.
* Final line ends with `\n`.
* Tests verify exact formatting.

---

## Task 029: Add output-file error handling
- [ ] Taken
- [ ] Done

**Goal:**
Prevent crashes when output writing fails.

**Description:**
Handle invalid output paths, permission errors, and file writing errors gracefully.

**Acceptance criteria:**
* Invalid output path produces a readable error.
* Permission error produces a readable error.
* No raw traceback is shown for expected file errors.
* Tests cover at least one output failure case.

---

<br>

# Phase 8 — Visual Representation

## Task 030: Choose and document renderer strategy
- [ ] Taken
- [ ] Done

**Goal:**
Decide whether the mandatory renderer will be terminal ASCII or MLX.

**Description:**
The subject allows either terminal ASCII rendering or graphical MLX rendering. The visual must clearly show walls, entry, exit, and the solution path. 

**Recommendation:**
Implement terminal ASCII first because it is simpler, easier to test, and safer for CI. Add MLX later only if time allows.

**Acceptance criteria:**
* `docs/architecture.md` states the chosen renderer.
* README explains how to use it.
* Renderer implementation is separated from maze generation.

---

## Task 031: Implement static ASCII renderer
- [ ] Taken
- [ ] Done

**Goal:**
Display the maze in the terminal.

**Description:**
Render:
```text
walls
entry
exit
42 pattern
optional path overlay
```

**Acceptance criteria:**
* Maze walls are visually understandable.
* Entry is clearly marked.
* Exit is clearly marked.
* Solution path can be drawn when requested.
* Pattern cells are visually visible.
* Renderer does not modify maze data.
* Tests verify renderer output for a small known maze.

---

## Task 032: Implement visual color themes
- [ ] Taken
- [ ] Done

**Goal:**
Support changing maze wall colors.

**Description:**
The subject requires user interaction to change maze wall colours. 

**Acceptance criteria:**
* At least two color themes exist.
* User can switch between themes.
* Color code is isolated from maze logic.
* Terminal output remains readable without color if needed.

---

## Task 033: Implement interactive display controller
- [ ] Taken
- [ ] Done

**Goal:**
Provide required visual interactions.

**Description:**
The subject requires interactions for:
```text
re-generate a new maze and display it
show/hide a valid shortest path
change maze wall colours
```

**Acceptance criteria:**
* User can regenerate a maze.
* User can show/hide the solution path.
* User can change wall colors.
* User can quit cleanly.
* Regeneration updates internal maze, solution, output file, and display.
* Invalid runtime errors are handled gracefully.

---

<br>

# Phase 9 — CLI Application Integration

## Task 034: Connect full application pipeline
- [ ] Taken
- [ ] Done

**Goal:**
Make the whole project run end-to-end.

**Description:**
Connect:
```text
argument parsing
config loading
config validation
maze generation
maze validation
solving
output writing
rendering
```

**Acceptance criteria:**
* `python3 a_maze_ing.py config.txt` runs the full project.
* A valid output file is created.
* The maze is displayed visually.
* Invalid configs fail cleanly.
* The code remains split into modules.

---

## Task 035: Add top-level error boundary
- [ ] Taken
- [ ] Done

**Goal:**
Prevent unexpected crashes during evaluation.

**Description:**
Add a clean top-level error boundary around the application. Expected project errors should become readable user messages.

**Acceptance criteria:**
* Expected errors do not show tracebacks.
* Developer/debug mode can optionally show tracebacks.
* Exit codes are sensible:
  * `0` for success
  * non-zero for user/config/runtime error
* Tests cover CLI failure cases.

---

<br>

# Phase 10 — Tests

## Task 036: Add config parser tests
- [ ] Taken
- [ ] Done

**Goal:**
Protect config parsing from regressions.

**Acceptance criteria:**
* Valid config test passes.
* Missing key test passes.
* Invalid width/height tests pass.
* Invalid coordinate tests pass.
* Invalid boolean test passes.
* Comment and blank-line tests pass.

---

## Task 037: Add maze generator tests
- [ ] Taken
- [ ] Done

**Goal:**
Verify generator correctness.

**Acceptance criteria:**
* Generated dimensions are correct.
* Same seed produces same maze.
* Different seeds can produce different mazes.
* Borders stay closed.
* Entry and exit are reachable.
* Pattern cells remain closed.
* Wall coherence passes.

---

## Task 038: Add solver tests
- [ ] Taken
- [ ] Done

**Goal:**
Verify shortest-path behavior.

**Acceptance criteria:**
* Solver finds path in simple maze.
* Solver returns shortest path.
* Solver returns correct direction letters.
* Solver rejects unsolvable maze cleanly.
* Solver does not walk through walls.

---

## Task 039: Add output writer tests
- [ ] Taken
- [ ] Done

**Goal:**
Verify exact Moulinette-style output.

**Acceptance criteria:**
* Known small maze exports exactly as expected.
* Hex digits are correct.
* Empty line is present.
* Entry and exit are formatted correctly.
* Path line is correct.
* All lines end with `\n`.

---

## Task 040: Add integration tests
- [ ] Taken
- [ ] Done

**Goal:**
Test the real program behavior.

**Description:**
Run the full application using example config files.

**Acceptance criteria:**
* Valid config produces output file.
* Output file passes internal validation.
* Invalid config exits cleanly.
* Missing config exits cleanly.
* Tests can run in CI without manual interaction.

---

<br>

# Phase 11 — Reusable Package Build

## Task 041: Prepare `mazegen` package source
- [ ] Taken
- [ ] Done

**Goal:**
Make the generator installable and reusable.

**Description:**
The subject requires the reusable module to be available for later installation by `pip`, with package file named `mazegen-*` located at the root of the repository. It also requires all elements needed to rebuild the package from source. 

**Acceptance criteria:**
* `mazegen` source package exists.
* `MazeGenerator` can be imported outside the main app.
* Package does not depend on renderer or CLI.
* Package has its own minimal documentation.
* Build configuration exists.

---

## Task 042: Build wheel or source distribution
- [ ] Taken
- [ ] Done

**Goal:**
Produce the required installable package artifact.

**Description:**
Build either:
```text
mazegen-<version>.tar.gz
```
or:
```text
mazegen-<version>-py3-none-any.whl
```
The subject allows both `.tar.gz` and `.whl`. 

**Acceptance criteria:**
* Built package file is located at repository root.
* Package name starts with `mazegen-`.
* Package can be installed in a fresh virtual environment.
* After installation, `MazeGenerator` can be imported and used.
* README explains how to rebuild the package.

---

<br>

# Phase 12 — Documentation

## Task 043: Write required README structure
- [ ] Taken
- [ ] Done

**Goal:**
Satisfy all README requirements.

**Description:**
The README must start with an italicized line:
```markdown
*This project has been created as part of the 42 curriculum by <login1>, <login2>.*
```
It must include Description, Instructions, Resources, config format, algorithm choice, why the algorithm was chosen, reusable code explanation, team roles, planning, what worked well, what could be improved, and tools used.  

**Acceptance criteria:**
* First README line is exactly italicized as required.
* Description section exists.
* Instructions section exists.
* Resources section exists.
* Config file format is fully documented.
* Algorithm choice is documented.
* Reusable package is documented.
* Team/project-management section exists.
* AI usage is honestly described.

---

## Task 044: Document reusable generator usage
- [ ] Taken
- [ ] Done

**Goal:**
Explain how future projects can reuse the generator.

**Description:**
The subject requires documentation showing how to instantiate and use the generator, pass custom parameters, access generated structure, and access at least a solution. 

**Acceptance criteria:**
* README contains a minimal code example.
* Example shows custom width, height, seed, entry, and exit.
* Example shows how to access maze structure.
* Example shows how to access solution path.
* Example works if copied into a Python file.

---

## Task 045: Maintain project-management notes
- [ ] Taken
- [ ] Done

**Goal:**
Prepare the README team-management section gradually instead of at the last minute.

**Description:**
Track:
```text
team member roles
initial plan
how the plan changed
what worked well
what could be improved
tools used
```

**Acceptance criteria:**
* Notes are updated after major milestones.
* Final README has honest project-management reflection.
* Both team members contribute to this section.

---

<br>

# Phase 13 — Final Quality and Evaluation Readiness

## Task 046: Run full mandatory quality gate
- [ ] Taken
- [ ] Done

**Goal:**
Confirm that the project satisfies mandatory technical checks.

**Acceptance criteria:**
* `make clean` works.
* `make install` works in a fresh environment.
* `make lint` passes.
* `make run` works.
* `python3 a_maze_ing.py config.txt` works.
* Output file is produced.
* Visual display works.
* No normal user path produces a traceback.

---

## Task 047: Perform manual invalid-input testing
- [ ] Taken
- [ ] Done

**Goal:**
Make sure the project does not crash during peer evaluation.

**Test cases:**
```text
missing config file
empty config file
bad KEY=VALUE syntax
missing WIDTH
WIDTH=abc
WIDTH=-1
ENTRY outside maze
EXIT outside maze
ENTRY == EXIT
PERFECT=banana
unwritable output path
too-small maze for 42 pattern
```

**Acceptance criteria:**
* Every case produces a clear error.
* No case produces an unexpected traceback.
* Error messages are understandable to a peer evaluator.

---

## Task 048: Peer-evaluation rehearsal
- [ ] Taken
- [ ] Done

**Goal:**
Prepare for the defense and possible live modification.

**Description:**
The subject says evaluators may ask for a small behavior change, function rewrite, display update, or data-structure adjustment to verify understanding. 

**Acceptance criteria:**
* Both team members can explain the generator.
* Both team members can explain wall bits.
* Both team members can explain BFS solving.
* Both team members can explain output format.
* Both team members can run lint and tests.
* Both team members can make a small change without breaking the project.

---

<br>

# Phase 14 — Optional Bonuses

## Task 049: Add second maze generation algorithm
- [ ] Taken
- [ ] Done

**Goal:**
Implement a bonus feature.

**Description:**
The subject lists multiple maze generation algorithms as a possible bonus. 

**Acceptance criteria:**
* Config supports algorithm selection.
* At least two algorithms are available.
* Both algorithms pass the same validation pipeline.
* README documents the algorithms and why they differ.

---

## Task 050: Add generation animation
- [ ] Taken
- [ ] Done

**Goal:**
Implement optional animated rendering.

**Description:**
The subject lists animation during maze generation as a possible bonus. 

**Acceptance criteria:**
* Animation can be enabled/disabled.
* Animation does not break CI tests.
* Static rendering still works.
* README documents the feature.

---


The End