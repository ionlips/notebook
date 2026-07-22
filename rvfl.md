---
date: 2026-07-17
keywords: [python, uv]
---
# uv

Run the following to see what Python versions are available and installed:

```shell
uv python list
```

One of my options were as follows:

```shell
cpython-3.12.13-macos-aarch64-none                  /Users/ilipsiuc/.local/share/uv/python/cpython-3.12-macos-aarch64-none/bin/python3.12
```

Thus, in order to create a virtual environment using this version, I just have
to specify the following:

```shell
uv venv --python 3.12.13
```

If your project already has a `requirements.txt` file, installing them via uv
is easy. First, ensure you have a `pyproject.toml` file:

```shell
uv init --bare
```

We specify `--bare` to ensure that no other files are created. After this is
done, you can install the dependencies as follows:

```shell
uv add -r requirements.txt
```
