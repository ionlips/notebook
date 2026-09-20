---
date: 2026-09-14
keywords: [python, uv]
---
# uv

Run `uv init --bare` in order to create a simple `pyproject.toml` file.

`uv venv` should be run after to create a virtual environment - activate it
using `source .venv/bin/activate`.

> [!NOTE]
> If you do not want a `pyproject.toml` file, simply run `uv venv`, activate
> it, and then use `uv pip install` to install packages. This will not work if
> you choose to use `uv add` since that requires a `pyproject.toml` file.

`uv add --group group package` will install `package` into a dependency group
called `group`. One can then run `uv sync --group group --no-default-groups` to
install the packages in `group`, avoiding a polluted environment.
