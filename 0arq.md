---
date: 2026-07-20
keywords: [macos]
---
# Delete all .DS_Store files

I often find myself annoyed at the random `.DS_Store` files created when
entering a directory within Finder. As per [this answer on Stack Exchange], run
the following:

```shell
sudo find / -name ".DS_Store" -exec rm {} \;
```

[this answer on Stack Exchange]: <https://apple.stackexchange.com/questions/284467/how-to-set-finder-to-always-use-list-view>
