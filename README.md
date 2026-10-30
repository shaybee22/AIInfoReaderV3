# AI Info Reader v3

**Click any AI image or video → see how it was made.**

AI Info Reader is a single HTML file that runs entirely in your browser. Point it at a folder and it shows the prompt, model, LoRAs, seed, sampler and the rest of the generation settings stored inside each file. It reads metadata written by **ComfyUI** (API prompt + UI workflow) and **A1111 / Forge / SwarmUI**, from **PNG, JPEG, WebP, MP4/MOV and WebM**.

- **Offline and local-first.** No install, no server, no account, no uploads. Nothing leaves your machine.
- **One file.** Download `AIInfoReader_v3.html` and open it. That's it.
- **Read-first.** Your original files are never modified.

---

## Features

- **Saved folders.** Add your output folders once and switch between them with one click. In Chrome/Edge, folder access is remembered between sessions.
- **Thumbnail grid** for images *and* videos. Videos get a poster frame, a duration badge and a muted preview on hover.
- **Badges** on every card:
  ⚙️ ComfyUI · 📝 A1111 / Forge / Swarm · ✍️ manual info · ⚠️ no metadata · 🗒️ has your notes · 🎬 video
- **Filters and search.** Filter by type (image/video), metadata source and model. Search across prompts, models, LoRAs, notes and file names.
- **Detail panel.** Large preview (image, or video with controls and loop), plus:
  - Model, LoRAs with strengths, positive and negative prompts
  - Seed, steps, CFG, sampler, scheduler, resolution
  - Duration, frame count and fps for videos
  - A **Copy** button on every field
  - Collapsible raw workflow / API-prompt JSON with node counts
- **Manual info for files with no metadata** (e.g. Civitai downloads):
  - Enter the settings yourself, or paste Civitai/A1111 "generation data" text (or ComfyUI JSON) and let it fill the form.
  - Add a source URL and notes, and optionally attach a `workflow.json`.
  - Saved as a small `<filename>.info.json` sidecar next to the file; it loads automatically with a ✍️ badge.
  - Embedded metadata always wins. On files that already have it, the sidecar can only add notes and a source link.
- **Exports** (each one writes a new file):
  - **Workflow** → `<name>_workflow.json`, ready to drag into ComfyUI.
  - **Settings** → `<name>_settings.json` and a readable `<name>_settings.txt`.
  - **Clean copy** → `<name>_clean.<ext>` with generation metadata stripped and **no re-encoding**, so pixels and frames are identical:
    - PNG: drops `tEXt` / `iTXt` / `zTXt` / `eXIf` chunks
    - JPEG: drops EXIF/XMP (APP1), APP13 and comment segments
    - WebP: drops EXIF/XMP chunks and fixes the RIFF size and VP8X flags
    - MP4/MOV: neutralises `udta`/`meta` boxes in place, so sizes and offsets are unchanged
    - WebM: turns the `Tags` element into a `Void` element of the same size

    Before saving, every clean copy is re-read to confirm no metadata is left, and test-opened (images) or test-played (videos). If either check fails, nothing is saved.
  - **PNG with info** → `<name>_with_info.png`: for PNGs you described by hand, a copy with your info embedded as an A1111-style `parameters` chunk. It's read back and checked before saving.
- **Copy image / Copy current frame** puts a PNG on the clipboard, ready to paste into Paint, Photoshop, PSP and so on.
- **Drag out.** Drag a card, the preview or the ⠿ handle onto the desktop or an Explorer window to drop the original file (Chrome/Edge).
- **Save copy as…** saves an exact copy of the original anywhere you choose.

## How to use

### Quick start

1. Download **`AIInfoReader_v3.html`** from this repo (open it, then click **Download raw file**).
2. Open it in **Chrome or Edge**: double-click it, or drag it into a browser window.
3. Click **📂 Manage Folders → 📁 Browse…** and pick a folder of images and videos, e.g. your ComfyUI `output` folder.
4. Give it a name and click **💾 Save**. The folder loads straight away, and its button appears along the top for next time.
5. Click any thumbnail to open the detail panel.
6. For a file showing ⚠️, click **✍️ Add Info** to record how it was made.
7. Use **⬇ Export** in the detail panel for workflows, settings, clean copies and PNG-with-info.

The first time you add info or export something in a folder, the browser asks once for permission to save files there.

### Browsers

| | Chrome / Edge | Firefox |
|---|---|---|
| Browse, filter, detail panel, copy | ✅ | ✅ |
| Folders remembered between sessions | ✅ | ❌ you pick the folder each session |
| Sidecars and exports saved into the folder | ✅ | ⬇ downloaded instead; move sidecars next to their files yourself |
| Drag a file out to the desktop | ✅ | ❌ |

### Optional: serve it locally

Opening the file directly works. If you'd rather serve it from `http://localhost`, run this in the folder that contains the HTML file:

```
python -m http.server 8000
```

Then open <http://localhost:8000/AIInfoReader_v3.html>. It still runs entirely on your machine.

## Safety

- **Originals are never opened for writing.** The app only reads your images and videos.
- The only files it writes are:
  - new export files in an **`exports/`** subfolder of the current folder, never overwriting anything (a repeat export becomes `name (1).ext`), and
  - your own `<filename>.info.json` sidecars, when you add info. "Remove info" deletes only that sidecar.
- If the browser can't write to the folder (Firefox, or permission declined), files are downloaded or saved through a save dialog instead.
- Nothing is uploaded anywhere. Saved-folder settings live in your browser's local storage.

## Limitations

- **Top-level folder scan only.** Subfolders aren't included; add them as separate folders.
- **Clean videos keep encoder info.** Clean copies remove generation metadata (prompts, workflows, settings). The encoder's name ("Lavf"/"Lavc"), the file-type brands and track names stay, because they live in structures a video needs in order to play. x264 also writes its own encoder settings into the video stream, which can't be removed without re-encoding.
- **JPEG rotation.** Stripping EXIF also removes the orientation tag. If a JPEG relied on it, the copy may appear rotated; the app warns you when this happens.
- **Some files simply have no metadata.** Images shared through Discord, social media, many websites and editors are often re-encoded or stripped. That's what manual info is for.
- **Workflow-only ComfyUI files.** If a file has only the UI workflow (no API prompt), its settings can't be resolved into fields, but the workflow can still be viewed and exported.
- **PNG with info** is PNG-only for now. Text outside Latin-1 (e.g. "café ☕") is stored in an `iTXt` chunk, as A1111 does; a few older tools only read `tEXt`.
- **Video playback** depends on the browser's codecs. For example, HEVC `.mov` files may not play in every browser, but their metadata can still be read.

## Keyboard shortcuts

| Key | Action |
|---|---|
| `/` | Jump to search |
| `Enter` / `Space` | Open the focused card |
| `←` / `→` | Previous / next file in the detail panel |
| `Ctrl+C` | Copy image (or current video frame) in the detail panel |
| `Esc` | Close the export menu, form or detail panel |
| `Ctrl+Enter` | Save in the Add/Edit Info form |

## Author

James Powell ([shaybee22](https://github.com/shaybee22))

## License

[MIT](LICENSE) © 2026 James Powell
