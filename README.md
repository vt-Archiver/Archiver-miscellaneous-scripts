# Archiver-miscellaneous-scripts 🛠️

Small, one-off helpers that don’t fit the big “Twitch / YouTube / Website” repos
but are still indispensable while maintaining an offline archive.

All tools are **stand-alone** (`python .../script.py`) and assume the shared folder
layout used across the vt-Archiver family:

```
persons/<streamer>/<platform>/<section>/<YYYY-MM-DDThh-mm-ssZ>*<id>*<title>/
```

| Script | Location | Purpose | Notes |
|--------|----------|---------|-------|
| **`sort_archive.org_VTuberArchive.py`** | `archive.org/` | Unpacks a ZIP grabbed from **archive.org**, verifies MD5s, then rebuilds the canonical vt-Archiver folder (`vod.mp4`, chat → SQLite, thumbnails, `metadata.vod.json`, `events.vod.json`). | Edit the three variables at the top of the file to properly use the script (`STREAMER`, `ARCHIVER_ROOT`, `ZIP_PATH`). |
| **`pack_hls.py`** | `hls.mp4.convertion/` | Converts a `vod.mp4` into **single-file HLS** (`hls/vod.m3u8` ➜ `hls/vod.mp4`) and removes the source MP4 afterwards. | Requires **ffmpeg** in `PATH`. |
| **`streamtracker_gui.py`** | `archival_progress/` | Tkinter GUI for tracking which streams are already backed up. Stores rows in `progress.sqlite`. | Dark / light toggle ☾/☀, resizable columns, layout saved to `layout.json`. |

---

## Requirements

* Python **3.11+**
* `ffmpeg` accessible through `PATH` (only for **pack_hls.py**)
* No third-party Python packages – everything is standard-library

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install --upgrade pip
````

---

## Quick usage

### 1 · Import a ZIP from *archive.org*

```bash
# edit STREAMER / ARCHIVER_ROOT / ZIP_PATH in the header first
python archive.org/sort_archive.org_VTuberArchive.py
```

Resulting structure:

```
persons/<streamer>/twitch/vods/2024-05-01T17-03-00Z_v2165656626_<title>/
│─ vod.mp4
│─ metadata.vod.json
│─ events.vod.json
│─ chat.vod.sqlite
└─ thumbnails/
   ├─ thumbnail_main.jpg
   └─ thumbnail_XXXX.jpg …
```

### 2 · Package a VOD into single-file HLS

```bash
python hls.mp4.convertion/pack_hls.py "D:\Archiver\...\vod.mp4"
# --hls_time defaults to 6-second segments
```

Creates:
```
hls/
  ├─ vod.m3u8   (playlist)
  └─ vod.mp4    (fragmented MP4 referenced by the playlist)
```
The original `vod.mp4` is deleted to save space.

### 3 · Track archive progress

```bash
python archival_progress/streamtracker_gui.py
```

* Paste a **TwitchTracker snippet** and hit *Parse snippet* – date & title
  auto-fill.
* Save: the entry persists in `progress.sqlite`.

---

## Repo layout

```
Archiver-miscellaneous-scripts/
├─ archive.org/
│   └─ sort_archive.org_VTuberArchive.py
├─ hls.mp4.convertion/
│   └─ pack_hls.py
└─ archival_progress/
    └─ streamtracker_gui.py
```
