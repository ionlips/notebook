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

On GNU/Linux, download the [release
binary](https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp) and
make it executable.

## Download music from YouTube

Ensure that you are within a virtual environment with yt-dlp-ejs installed and
run the following:

```shell
yt-dlp --audio-format opus --extract-audio --js-runtime node "{{url}}"
```

> [!NOTE]
> In order to upload to Spotify as a local file, ensure to use `--audio-format
> mp3` since `opus` is not recognised.

If you get an error regarding YouTube requiring you to sign in, pass the
`--cookies-from-browser firefox` flag.

## Download playlists from YouTube

Ensure that you are within a virtual environment with yt-dlp-ejs installed and
run the following:

```shell
yt-dlp --js-runtime node --output "%(playlist_title)s/%(playlist_index)s - %(title)s.%(ext)s" "{{url}}"
```

## Download single short/video from YouTube

Ensure that you are within a virtual environment with yt-dlp-ejs installed and
run the following:

```shell
yt-dlp --js-runtime node --output "%(title)s.%(ext)s" "{{url}}"
```

## Save videos as MP4 instead of WebM

````ai
Add `-t mp4`. This preset needs a reasonably recent yt-dlp and ffmpeg installed:

```bash
yt-dlp --js-runtime node -t mp4 --output "%(title)s.%(ext)s" "{{url}}"
```

It prefers H.264 video and AAC audio and saves the result as `.mp4`. Most players and editors open that combination without trouble.

**Other options:**

- **Change the container only:** `--remux-video mp4` (or `--merge-output-format mp4`) moves the existing VP9 or AV1 and Opus streams into an `.mp4` without re-encoding. It's fast and keeps the original quality, but some players and editors (older QuickTime, some TVs) can't play those codecs inside an MP4.
- **Pick native MP4 formats up front:**
  ```bash
  -f "bv*[ext=mp4]+ba[ext=m4a]/b[ext=mp4]/b"
  ```
  YouTube often has a higher resolution in VP9 or AV1 than in H.264, so this may give you lower quality.
- **Re-encode:** `--recode-video mp4` always produces a compatible MP4, but it's slow and loses a little quality. Only use it if the options above don't work for your player.

If `-t mp4` gives an error, update yt-dlp with `yt-dlp -U` (or through pip or your package manager, depending on how you installed it).
````
