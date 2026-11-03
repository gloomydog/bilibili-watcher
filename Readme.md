# bilibili_watcher

Play Bilibili videos directly from the CDN with mpv, or save them as mp4.

## Description

Bilibili's anti-bot protection often blocks the usual tools:

- **yt-dlp** fails with `HTTP Error 412: Precondition Failed`, because it scrapes the video's HTML page, which is heavily protected.
- **mpv** with a Bilibili URL uses yt-dlp internally, so it fails the same way.
- **mpv with raw CDN URLs** often loses audio after a few seconds: Akamai / PCDN hosts drop the connection mid-stream.

This script avoids those problems:

- It calls the JSON APIs directly (`view`, `playurl` with WBI signing) instead of scraping HTML, with `buvid3` / `buvid4` device cookies.
- It picks reliable `upos-*.bilivideo.com` CDN hosts.
- It passes the required `Referer` to mpv.

Bilibili serves video and audio as separate `.m4s` (DASH) streams. mpv plays them together without merging; `--dl` merges them with ffmpeg.

## Usage

```bash
./bilibili_watcher.py "https://www.bilibili.com/..."              # play
./bilibili_watcher.py "URL" -s 1:30 -e 2:45                       # play a section
./bilibili_watcher.py "URL" --dl -s 1:30 -e 2:45 -o clip          # save clip.mp4
```


| Option | Description |
|---|---|
| `--dl` | Save as mp4 instead of playing |
| `-s` / `-e` | Start / end time (`90`, `1:30`, `00:01:30.5`) |
| `-o` / `-d` | Output file / output folder (`--dl`) |
| `--audio-only` / `--video-only` | Save audio only (`.m4a`) / video only (`--dl`) |
| `--precise` | Frame-accurate cut by re-encoding (slow) |
| `--print` | Print stream URLs only |
| `--sessdata` | `SESSDATA` login cookie for 1080p+ (or env `BILI_SESSDATA`) |
| `--cdn HOST` | Force a CDN host, e.g. `upos-sz-mirrorali.bilivideo.com` |
| `-- ...` | Extra args passed to mpv, e.g. `-- --hwdec=vaapi` |

## Notes

- CDN URLs expire after a few hours.
- Bilibili changes its APIs and protection from time to time, so the script may break.
