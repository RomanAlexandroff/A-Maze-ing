*This project has been created as part of the 42 curriculum by login0, roaleksa.*

# A-Maze-ing

## Description




<br>

## Instructions




<br>

## Usage




<br>

## Project Structure

```text
a_maze_ing/
│
├── .github/
│   ├── workflows/
│   │   └── CI.yml					-- script to run GitHub Actions CI
│   │
│   ├── pull_request_template.md	-- for Pull Requests
│   └── CODEOWNERS					-- for Pull Requests
│
├── docs/
│   ├── backlog.md					-- project tasks to work on (instead of Jira)
│   ├── project_workflow.md			-- instructions how to work with Git
│   └── subject.pdf					-- original school project subject
│
├── examples/
│   ├── default.cfg
│   ├── perfect.cfg
│   └── imperfect.cfg
│
├── src/
│   ├── __init__.py
│   ├── main.py
│   │
│   ├── parser/
│   │   ├── parser.py
│   │   └── validator.py
│   │
│   ├── maze/
│   │   ├── maze.py
│   │   ├── cell.py
│   │   └── grid.py
│   │
│   ├── generator/
│   │   ├── dfs.py
│   │   └── prim.py
│   │
│   ├── solver/
│   │   └── bfs.py
│   │
│   ├── renderer/
│   │   ├── ascii_renderer.py
│   │   └── gui_renderer.py
│   │
│   ├── exporter/
│   │   └── export.py
│   │
│   └── utils/
│       ├── exceptions.py
│       └── random_utils.py
│
├── tests/
│   ├── test_parser.py
│   ├── test_generator.py
│   ├── test_solver.py
│   └── test_export.py
│
├── .flake8							-- script to setup Flake8 behaviour
├── .gitignore
├── Makefile
├── mypy.ini						-- script to setup MyPy behaviour
├── pyproject.toml
├── README.md						-- this document
└── requirements-dev.txt			-- for the CI jobs
```

<br>

## Resources



<br>

## AI Usage Disclosure

- To create the initial version of the project Backlog
