---
date: 2026-08-27
keywords: [rust]
---
# Install Rust

```shell
export RUSTUP_HOME=$XDG_DATA_HOME/rustup
export CARGO_HOME=$XDG_DATA_HOME/cargo
curl --proto =https --show-error --silent --tlsv1.2 -fail https://sh.rustup.rs \
    | sh -s -- --no-modify-path --profile minimal -y
```
