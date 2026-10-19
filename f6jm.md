---
date: 2026-09-13
keywords: [yt-dlp]
---
# yt-dlp

## Download and installation

Download for macOS as follows:

```shell
curl --location --output ~/.local/bin/yt-dlp "https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp_macos"
chmod +x ~/.local/bin/yt-dlp
```

Download FFmpeg and FFprobe.

## Download music from YouTube

Ensure that you are within a virtual environment with yt-dlp-ejs installed.

```shell
yt-dlp --audio-format opus --extract-audio --js-runtime node "{{url}}"
```

> [!NOTE]
> In order to upload to Spotify as a local file, ensure to use `--audio-format
> mp3` since `opus` is not recognised.

If you get an error regarding YouTube requiring you to sign in, pass the
`--cookies-from-browser firefox` flag.

## Download playlists from YouTube

Ensure that you are within a virtual environment with yt-dlp-ejs installed.

```shell
yt-dlp --js-runtime node --output "%(playlist_title)s/%(playlist_index)s - %(title)s.%(ext)s" "{{url}}"
```

## Download single short/video from YouTube

Ensure that you are within a virtual environment with yt-dlp-ejs installed.

```shell
yt-dlp --js-runtime node --output "%(title)s.%(ext)s" "{{url}}"
```
