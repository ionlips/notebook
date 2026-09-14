---
date: 2026-09-14
keywords: [python, uv]
---
# uv

Run `uv init --bare` in order to create a simple `pyproject.toml` file.

`uv venv` should be run after to create a virtual environment - activate it
using `source .venv/bin/activate`.

`uv add --group group package` will install `package` into a dependency group
called `group`. One can then run `uv sync --group group --no-default-groups` to
install the packages in `group`, avoiding a polluted environment.
