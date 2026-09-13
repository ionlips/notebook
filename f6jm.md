---
date: 2026-09-13
keywords: [yt-dlp]
---
# yt-dlp

Download audio from YouTube as follows (ensuring you are within a virtual
environment where yt-dlp-ejs is installed):

```shell
yt-dlp --audio-format opus --js-runtime node -x {{url}}
```

> [!NOTE]
> In order to upload to Spotify as a local file, ensure to use `--audio-format
> mp3` since `opus` is not recognised.

If you get an error regarding YouTube requiring you to sign in, pass the
`--cookies-from-browser firefox` flag.
