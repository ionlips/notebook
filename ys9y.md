---
date: 2026-09-20
keywords: [yt-dlp]
---
# Download YouTube playlists using yt-dlp

```shell
yt-dlp --js-runtime node --output "%(playlist_title)s/%(playlist_index)s - %(title)s.%(ext)s" "{{url}}"
```
