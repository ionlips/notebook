---
date: 2026-09-07
keywords: []
---
# dotfiles for unsupported glibc

Tree-sitter's CLI version 0.26.11 does not work on systems whose glibc is not
supported (i.e., qllm has glibc version 2.34, as does MeluXina). To fix this,
`.chezmoiexternal.toml.tmpl` skips the installation of it, and one must use
`cargo-binstall` to install it. First, make sure to install Rust via `rustup`:

> [!NOTE]
> Add `export $RUSTUP_HOME=$XDG_DATA_HOME/rustup` to your `.rc` file(s) for it
> to persist.

```shell
export $RUSTUP_HOME=$XDG_DATA_HOME/rustup
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Make sure to modify the installation so that it doesn't modify `PATH`. Proceed
to install `cargo-binstall` as follows:

> [!NOTE]
> Add `export $CARGO_HOME=$XDG_DATA_HOME/cargo` to your `.rc` file(s) for it to
> persist.

```shell
export $CARGO_HOME=$XDG_DATA_HOME/cargo
curl -L --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash
```

Once installed, proceed to install Tree-sitter's CLI as follows:

```shell
cargo binstall tree-sitter-cli@0.25.10
```

Neovim should work fine now.
