---
date: 2026-07-21
keywords: [dotfiles, stow]
---
# Install software locally

Ensure that Stow is installed first, as follows:

```shell
# Create the required directories.
mkdir --parents $HOME/.local/opt $HOME/.local/src/stow

# Download and extract.
curl --location --output $HOME/.local/src/stow/2.4.1.tar.gz "https://ftp.gnu.org/gnu/stow/stow-2.4.1.tar.gz"
tar --directory $HOME/.local/src/stow --extract --file $HOME/.local/src/stow/2.4.1.tar.gz --one-top-level 2.4.1 --strip-components 1

# Configure and install.
mkdir --parents $HOME/.local/src/stow/2.4.1/build && cd $_
../configure --prefix $HOME/.local
make && make install
```

## Autotools

```shell
# Set variables.
PKG=foo VER=1.2.3

# Create the required directories.
mkdir --parents $HOME/.local/src/$PKG

# Download and extract.
curl --location --output $HOME/.local/src/$PKG/$VER.tar.gz "{{url}}"
tar --directory $HOME/.local/src/$PKG --extract --file $HOME/.local/src/$PKG/$VER.tar.gz --one-top-level $VER --strip-components 1

# Configure and install.
mkdir --parents $HOME/.local/src/$PKG/$VER/build && cd $_
../configure --prefix $HOME/.local/opt/$PKG-$VER
make --jobs $(nproc) && make install

# Symlink via Stow.
stow --dir $HOME/.local/opt --target $HOME/.local $PKG-$VER
```

## CMake

```shell
# Set variables.
PKG=foo VER=1.2.3

# Create the required directories.
mkdir --parents $HOME/.local/src/$PKG

# Download and extract.
curl --location --output $HOME/.local/src/$PKG/$VER.tar.gz "{{url}}"
tar --directory $HOME/.local/src/$PKG --extract --file $HOME/.local/src/$PKG/$VER.tar.gz --one-top-level $VER --strip-components 1

# Configure and install.
cd $HOME/.local/src/$PKG/$VER
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HOME/.local/opt/$PKG-$VER
cmake --build build --parallel $(nproc)
cmake --install build

# Symlink via Stow.
stow --dir $HOME/.local/opt --target $HOME/.local $PKG-$VER
```
