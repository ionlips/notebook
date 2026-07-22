---
date: 2026-07-21
keywords: [linux]
---
# Configure non-root GNU/Linux system

1.  Clone <https://github.com/ionlips/dotfiles>.
1.  `git switch linux/non-root`.
1.  [Download Neovim] locally as per [Install software locally](njyq.md).[^1]
1.  [Install Starship].
1.  `make stow`.
1.  Compile Tree-sitter's CLI from source as per [Wednesday, July 22, 2026](journal/daily/2026-07-22.md).

> [!IMPORTANT]
> auto-dark-mode.nvim doesn't work if you aren't in a desktop environment.

[Download Neovim]: <https://neovim.io/doc/install/>
[Install Starship]: <https://starship.rs/faq/#how-do-i-install-starship-without-sudo>

[^1]: After downloading to `~/.local/src`, extract it as follows:

    ```shell
    tar xf ~/.local/src/neovim/0.12.4.tar.gz -C ~/.local/opt/neovim-0.12.4 --strip-components=1
    ```

    Ensure that `~/.local/opt/neovim-0.12.4` exists. Instead of `stow`, use
    `~/.local/bin/stow` since `.bashrc` is not yet symlinked.
