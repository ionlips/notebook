---
date: 2026-07-28
keywords: []
---
# SAM 3 on qllm

<https://huggingface.co/facebook/sam3> is the Hugging Face repo for it. One has
to apply for access before the model's weights can be used.

On qllm, I created a new directory within `/ichec/work/staff` called `ilipsiuc`
and ran `chmod 700 ilipsiuc` to make sure nobody else can access the directory.

Clone the official GitHub repo:

```shell
git clone https://github.com/facebookresearch/sam3.git
```

`cd` into `sam3` and run `uv sync`.

I ran into the following issue:

<!-- markdownlint-disable MD013 -->
```text
Using CPython 3.9.25 interpreter at: /usr/bin/python3
Creating virtual environment at: .venv
  × No solution found when resolving dependencies for split (markers: python_full_version == '3.8.*'):
  ╰─▶ Because the requested Python version (>=3.8) does not satisfy Python>=3.9.0 and numpy>=1.26.0,<=1.26.1 depends on Python>=3.9,<3.13, we can conclude that numpy>=1.26.0,<=1.26.1 cannot be used.
      And because only the following versions of numpy are available:
          numpy<=1.26.0
          numpy==1.26.1
          numpy==1.26.2
          numpy==1.26.3
          numpy==1.26.4
          numpy>=2
      we can conclude that numpy>=1.26.0,<1.26.2 cannot be used. (1)

      Because the requested Python version (>=3.8) does not satisfy Python>=3.9.0 and numpy>=1.26.2,<=1.26.4 depends on Python>=3.9, we can conclude that numpy>=1.26.2,<=1.26.4 cannot be used.
      And because we know from (1) that numpy>=1.26.0,<1.26.2 cannot be used, we can conclude that numpy>=1.26.0,<2 cannot be used.
      And because your project depends on numpy>=1.26,<2 and your project requires sam3[dev], we can conclude that your project's requirements are unsatisfiable.

hint: While the active Python version is 3.9, the resolution failed for other Python versions supported by your project. Consider limiting your project's supported Python versions using `requires-python`.
hint: The `requires-python` value (>=3.8) includes Python versions that are not supported by your dependencies (e.g., numpy>=1.26.0,<=1.26.1 only supports >=3.9, <3.13). Consider using a more restrictive `requires-python` value (like >=3.9, <3.13).
```
<!-- markdownlint-enable MD013 -->
