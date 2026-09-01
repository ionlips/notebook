---
date: 2026-08-27
keywords: [meluxina, neovim]
---
# Fix Node.js libatomic.so.1 link error on MeluXina

<!-- markdownlint-disable MD013 -->
```shell
$ node --version
node: error while loading shared libraries: libatomic.so.1: cannot open shared object file: No such file or directory
```
<!-- markdownlint-enable MD013 -->

To fix:

```shell
module load GCC/14.2.0
$ find $EBROOTGCCCORE -name 'libatomic.so.1*' 2>/dev/null
/apps/USE/easybuild/release/2025.1/software/GCCcore/14.2.0/lib64/libatomic.so.1.2.0
/apps/USE/easybuild/release/2025.1/software/GCCcore/14.2.0/lib64/libatomic.so.1
```

Copy the library:

<!-- markdownlint-disable MD013 -->
```shell
mkdir -p ~/.local/lib
cp \
    /apps/USE/easybuild/release/2025.1/software/GCCcore/14.2.0/lib64/libatomic.so.1 \
    ~/.local/lib/libatomic.so.1
```
<!-- markdownlint-enable MD013 -->
