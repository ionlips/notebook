---
date: 2026-07-21
keywords: [dotfiles, stow]
---
# Install software locally

Ensure that Stow is installed first, as follows:

```shell
# Create the required directories.
mkdir --parents ~/.local/opt ~/.local/src/stow

# Download and extract.
curl --location --output ~/.local/src/stow/2.4.1.tar.gz "https://ftp.gnu.org/gnu/stow/stow-2.4.1.tar.gz"
tar --directory ~/.local/src/stow --extract --file ~/.local/src/stow/2.4.1.tar.gz --one-top-level 2.4.1 --strip-components 1

# Configure and install.
mkdir --parents ~/.local/src/stow/2.4.1/build && cd $_
../configure --prefix $HOME/.local
make && make install
```

## Autotools

```shell
# Set variables.
PKG=foo VER=1.2.3

# Create the required directories.
mkdir --parents ~/.local/src/$PKG

# Download and extract.
curl --location --output ~/.local/src/$PKG/$VER.tar.gz "{{url}}"
tar --directory ~/.local/src/$PKG --extract --file ~/.local/src/$PKG/$VER.tar.gz --one-top-level $VER --strip-components 1

# Configure and install.
mkdir --parents ~/.local/src/$PKG/$VER/build && cd $_
../configure --prefix $HOME/.local/opt/$PKG-$VER
make --jobs $(nproc) && make install

# Symlink via Stow.
stow --dir ~/.local/opt --target ~/.local $PKG-$VER
```

## CMake

```shell
# Set variables.
PKG=foo VER=1.2.3

# Create the required directories.
mkdir --parents ~/.local/src/$PKG

# Download and extract.
curl --location --output ~/.local/src/$PKG/$VER.tar.gz "{{url}}"
tar --directory ~/.local/src/$PKG --extract --file ~/.local/src/$PKG/$VER.tar.gz --one-top-level $VER --strip-components 1

# Configure and install.
cd ~/.local/src/$PKG/$VER
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HOME/.local/opt/$PKG-$VER
cmake --build build --parallel $(nproc)
cmake --install build

# Symlink via Stow.
stow --dir ~/.local/opt --target ~/.local $PKG-$VER
```
