---
date: 2026-10-20
keywords: [ichec]
---
# MicroInspect

```shell
mkdir ~/dev/ichec/microinspect
cd ~/dev/ichec/microinspect
uv init --bare --python 3.12
uv venv --python 3.12
uv add open3d
git clone git@github.com:CVHub520/X-AnyLabeling.git
cd X-AnyLabeling
uv pip install -e ".[cpu]"
cd ..
```

X-AnyLabeling works so long as the virtual environment is activated.

X-AnyLabeling supports `.ply` and not `.obj`.

Decided to do most of my work within `.ipynb` files:

```shell
uv add jupyterlab
jupyter lab
```

Installed Vertopal CLI via `uv add vertopal`.

Decided to forget Vertopal CLI since it's a cloud service...

`scripts/obj2ply.py` converts the `.obj`s to `.ply`s.
