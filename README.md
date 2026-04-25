# ghdl — GitHub Download

Download anything into your repo just by writing a URL in a commit message.  
Files are stored in a separate `downloads` branch, organised by timestamp.

---

## Setup

1. Fork / copy this repo (or drop the workflow into your own `.github/workflows/`)
2. **Settings → Actions → General → Workflow permissions → Read and write permissions → Save**

No secrets or tokens needed.

---

## Usage

Put a URL anywhere in your commit message:

```bash
# Download a file
git commit -am "https://example.com/dataset.zip"

# Pull a Docker image and save as tar.gz
git commit -am "docker://ubuntu:22.04"

# Download a YouTube / media URL via yt-dlp
git commit -am "https://youtu.be/dQw4w9WgXcQ"

# Mix several targets in one commit
git commit -am "docker://nginx:latest https://example.com/model.bin https://youtu.be/xyz"
```

You can also trigger it from the GitHub web UI: edit any file, write the URL as the commit message, and commit directly to `main`.

---

## How it works

| Prefix / pattern | Tool | Output |
|---|---|---|
| `docker://image:tag` | `docker save \| gzip` | `docker_image__tag.tar.gz` |
| Known video site / media extension | `yt-dlp` | `Title.mp4` |
| Any other `http(s)://` URL | `aria2c` (16 connections) | original filename |

**Large files** (> 90 MB) are automatically split into 90 MB zip parts that GitHub will accept.  
To reassemble: `zip -s 0 file.zip --out full.zip && unzip full.zip`

All files land in the `downloads` branch under a `YYYY-MM-DD_HH-MM-SS/` folder, along with a `manifest.yml` that records the source branch, commit SHA, and file list.

---

## Supported media sites (yt-dlp auto-detected)

YouTube, Vimeo, Dailymotion, Twitch, TikTok, Instagram, Facebook, Twitter/X,  
Reddit, Bilibili, VK, Rutube, Streamable, Odysee, Rumble, Kick, and any direct  
`.mp4 .mkv .webm .flv .avi .mov .m3u8` URL.

---

## Notes

- URLs must be publicly accessible (no login required)
- Multiple URLs/images in one commit are all downloaded in the same run
- The workflow skips its own commits (`[skip ci]`) to avoid loops
- GitHub's free tier has a 1 GB soft repo limit — the `downloads` branch is orphaned so it doesn't bloat your code history
