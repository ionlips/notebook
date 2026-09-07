---
date: 2026-09-07
keywords: []
---
# dotfiles for unsupported glibc

Tree-sitter's CLI version 0.26.11 does not work on systems whose glibc is not
supported (i.e., qllm has glibc version 2.34, as does MeluXina). To fix this,
`.chezmoiexternal.toml.tmpl` skips the installation of it, and one must use
`cargo-binstall` to install it, as follows:

> [!NOTE]
> Add `export $CARGO_HOME=$XDG_DATA_HOME/cargo` to your `.rc` file(s) for it to
> persist.

```shell
export $CARGO_HOME=$XDG_DATA_HOME/cargo
curl -L --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash
```
