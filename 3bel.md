---
date: 2026-08-27
keywords: [meluxina, neovim]
---
# Install Tree-sitter's CLI on MeluXina

Ensure Rust is installed (do not use the versions available via Lmod since they
are too old). Once installed, run the following:

```shell
cargo install tree-sitter-cli \
    --locked \
    --no-default-features \
    --root $HOME/.local \
    --version 0.26.11
```
