---
date: 2026-07-21
keywords: [stow]
---
# Install software locally

Ensure that Stow is installed first, as follows:

```shell
# Create the required directories.
mkdir -p ~/.local/opt ~/.local/src/stow

# Download and extract.
curl -L https://ftp.gnu.org/gnu/stow/stow-2.4.1.tar.gz -o ~/.local/src/stow/2.4.1.tar.gz
tar \
    --one-top-level=2.4.1 \
    --strip-components=1 \
    -C ~/.local/src/stow \
    -f ~/.local/src/stow/2.4.1.tar.gz \
    -x

# Configure and install.
mkdir -p ~/.local/src/stow/2.4.1/build && cd $_
../configure --prefix=$HOME/.local
make && make install
```

## Autotools

```shell
# Set variables.
PKG=foo VER=1.2.3

# Create the required directories.
mkdir -p ~/.local/src/$PKG

# Download and extract.
curl -L {{url}} -o ~/.local/src/$PKG/$VER.tar.gz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C ~/.local/src/$PKG \
    -f ~/.local/src/$PKG/$VER.tar.gz \
    -x

# Configure and install.
mkdir -p ~/.local/src/$PKG/$VER/build && cd $_
../configure --prefix=$HOME/.local/opt/$PKG-$VER
make -j$(sysctl -n hw.ncpu) && make install

# Symlink via Stow.
stow -d ~/.local/opt -t ~/.local $PKG-$VER
```

## CMake

```shell
# Set variables.
PKG=foo VER=1.2.3

# Create the required directories.
mkdir -p ~/.local/src/$PKG

# Download and extract.
curl -L {{url}} -o ~/.local/src/$PKG/$VER.tar.gz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C ~/.local/src/$PKG \
    -f ~/.local/src/$PKG/$VER.tar.gz \
    -x

# Configure and install.
cd ~/.local/src/$PKG/$VER
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HOME/.local/opt/$PKG-$VER
cmake --build build -j$(sysctl -n hw.ncpu)
cmake --install build

# Symlink via Stow.
stow -d ~/.local/opt -t ~/.local $PKG-$VER
```
