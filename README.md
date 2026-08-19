<p align="center">
  <img src="icons/icon-192.png" width="128" alt="Sprint Repeat Log">
</p>

<h1 align="center">Sprint Repeat Log</h1>

<p align="center">
  Repeat-sprint interval log. One HTML file, no server, no accounts, works with the phone in flight mode.
</p>

---

A single-page app for one training protocol: **run hard for a fixed effort, rest, run again.**
A single fast rep tells you very little. How close the second rep gets to the first is the number
worth tracking, so the app is built around it.

**Fade** = the drop in speed from the first rep to the last, as a percentage of the first.
200 m in 60 s then 200 m in 63 s is a fade of 4.8%. Lower is fitter.

## What it does

- **Log** — up to four reps per session, distance and time on each, live pace and a fade bar
- **Timer** — runs the protocol out loud: 60 s effort, 3 min rest, 60 s effort, with countdown beeps, vibration and a screen wake lock. Two modes: fixed time (enter distance after) or fixed distance (tap Lap and it takes the time)
- **Charts** — first rep vs last rep speed, fade trend, session distance, effort, and a morning-versus-evening split
- **Report** — A4 print sheet with summary boxes, progression chart, session table, optional audit trail and signature block
- **Bulk import** — paste a block of cells straight out of Excel or Sheets, or a TSV block from a dictation session. Dry-run preview, inline fixing, one-tap undo
- **Backup** — JSON export that merges rather than overwrites, CSV export, rolling snapshots, and silent auto-save to a real file on Windows

Everything lives in IndexedDB on the device. No network calls at runtime, ever.

## Publish it on GitHub Pages

1. Create a repository and upload every file in this folder, keeping the `icons/` folder intact.
2. **Settings → Pages → Source: Deploy from a branch**, branch `main`, folder `/ (root)`.
3. Wait for the green tick, then open `https://<your-username>.github.io/<repo-name>/`.
4. On Android Chrome, tap the menu and choose **Install app** (or **Add to Home screen**).
   On Windows Chrome or Edge, use the install icon in the address bar.

The `.nojekyll` file stops GitHub from reprocessing the site. Leave it there.

### Files

| File | Why it's needed |
|---|---|
| `index.html` | The whole app — markup, styles, logic |
| `manifest.webmanifest` | Name, icons and `"id": "./"` so installs don't collide with other apps on the same Pages account |
| `sw.js` | Service worker. Caches the shell so the app opens offline, and is what makes Chrome offer a real install |
| `icons/` | 192 and 512 px icons, plus maskable versions that survive Android's circular crop |
| `logo.svg` | Full-size JNR brand mark, self-contained for linking elsewhere |
| `logo-jnr.png` | The same mark, sized for the app header |
| `.nojekyll` | Tells GitHub Pages to serve the files as they are |

### After you change `index.html`

Bump the `CACHE` constant at the top of `sw.js` (for example `sprintlog-v1.2.1`) and push both files.
Without the bump, the old cached copy keeps being served and your change never appears.

## Running it without GitHub

Open `index.html` directly from disk and everything works except installation and the service
worker — those need `http://` or `https://`. Your data is tied to where the app is opened from,
so pick one location and stay with it. Moving between `file://` and a Pages URL means two
separate databases: export a backup from one and import it into the other.

## Printing

In the print dialogue, expand **More settings** and turn **off** "Headers and footers", or Chrome
stamps its own URL and page numbers over the report. Android's print flow drops some page
settings — if the table crops, print the same report from a desktop browser.

## Data

| | |
|---|---|
| Storage | IndexedDB, four stores: `records`, `meta`, `log`, `backups` |
| Deletes | Soft only. Tombstones, so a restored backup can't resurrect a deleted session |
| Import | Merges on `id`. The newer `updatedAt` wins; ties go to the higher `rev` |
| Audit | Every create, edit, delete and import is written to an append-only log |
| Export | `sprintlog-backup-YYYY-MM-DD-HHmm.json`, plus CSV for spreadsheets |

Nothing leaves the device unless you export it yourself.
