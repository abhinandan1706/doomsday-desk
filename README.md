# Doomsday Desk

A small, fast notes app for Windows with a dark, cinematic look: black and charcoal panels, crimson accents, and an original "doomsday clock" emblem. Open it and start typing. There's no Save button; everything saves itself.

Built with Electron and plain HTML/CSS/JavaScript. It has no backend, no account, no internet connection, and no tracking. Your notes stay in a single file on your PC.

---

## Quick start

You need [Node.js](https://nodejs.org) 18 or newer (tested with Node 24 and npm 12).

```bash
npm install
```

```bash
npm start
```

The first `npm start` downloads the Electron runtime (about 100 MB), so it takes a minute. After that it launches in a second or two.

## Build a Windows installer

```bash
npm run dist
```

This creates **`dist\Doomsday Desk Setup 1.0.0.exe`**. Run it to install the app with Start menu and desktop shortcuts. You can also run the unpacked app directly from `dist\win-unpacked\Doomsday Desk.exe`.

To build a single portable `.exe` that runs without installing:

```bash
npm run dist:portable
```

> The build isn't code-signed, so Windows SmartScreen may show **"Windows protected your PC"** the first time. Click **More info → Run anyway**. That's normal for personal, unsigned apps.

---

## Features

| | |
|---|---|
| **Instant start** | The app opens on a fresh note with the cursor ready. Just type. |
| **Auto-save** | Every keystroke is saved to disk within a fraction of a second. Closing the window, switching notes, or quitting never loses text. |
| **Multiple notes** | Create, edit, delete, pin, and switch between notes. A note with no title uses its first line as the name. |
| **Search** | Searches titles and note text as you type, with highlighted matches. Several words narrow the results. |
| **Categories** | Four optional colour tags: Ideas, Study, Code, Reminders. **Code** notes switch to a monospace font and turn spell-check off. |
| **Pinning** | Pinned notes stay at the top of the list. |
| **Quick capture** | A small floating window you can open from anywhere with **Ctrl+Alt+N**. Type a thought and press Esc; it's already filed in your desk. |
| **System tray** | Closing the window keeps the app in the tray, so quick capture still works. Click the tray icon to reopen it. |
| **Undo delete** | Deleting shows an **Undo** button for a few seconds. |
| **Window memory** | The main and quick-capture windows reopen where you left them, at the same size. |
| **Adjustable text** | Ctrl + / − / 0 changes the editor font size, and the setting is remembered. |
| **Right-click menu** | Cut, copy, paste, select all, plus spelling suggestions. |
| **Tidy by default** | Empty notes are discarded automatically, so no blank clutter piles up. |
| **Reduced motion** | All animations turn off if Windows' "Animation effects" setting is off. |

### Layout

```
┌───────────┬──────────────┬──────────────────────────────┐
│ New note  │ Search       │ ● ● ● ● ●            📌  🗑   │
│           │──────────────│ Title                        │
│ All notes │ PINNED       │                              │
│ Pinned    │  Note …      │ Your note text…              │
│           │ NOTES        │                              │
│ Ideas     │  Note …      │                              │
│ Study     │  Note …      │                              │
│ Code      │              │                              │
│ Reminders │              │                              │
│           │              │──────────────────────────────│
│ Quick cap │              │ 42 WORDS · 230 CHARS  EDITED │
└───────────┴──────────────┴──────────────────────────────┘
  sidebar     note list      editor
```

On narrow windows the sidebar shrinks to an icon rail.

---

## Keyboard shortcuts

Press **Ctrl + /** inside the app to see these at any time.

### Main window

| Action | Shortcut |
|---|---|
| New note | **Ctrl + N** |
| Search notes | **Ctrl + F** |
| Previous / next note | **Alt + ↑ / ↓** |
| Pin / unpin | **Ctrl + P** |
| Delete note (undoable) | **Ctrl + Shift + Del** |
| Quick capture (works from any app) | **Ctrl + Alt + N** |
| Text size bigger / smaller / reset | **Ctrl + = / − / 0** |
| Show shortcuts | **Ctrl + /** |
| Close window (stays in tray) | **Ctrl + W** |
| Quit completely | **Ctrl + Q** |

In the search box, use **↑ / ↓** to move through results, **Enter** to open one, and **Esc** to clear the search. A second Esc returns to the editor.

In the title field, **Enter** jumps to the note body. **Tab** in the body inserts a tab, which is handy for code.

### Quick capture window

| Action | Shortcut |
|---|---|
| Done (close, already saved) | **Esc** or **Ctrl + Enter** |
| Start another note | **Ctrl + N** |
| Open this note in the full desk | **Ctrl + O** |
| Open / close from anywhere | **Ctrl + Alt + N** |

---

## Tray and background behaviour

- Clicking **X** or pressing **Ctrl+W** hides the window to the system tray. The first time, a short notification tells you so.
- On Windows 11, new tray icons start in the **hidden icons** area (the **^** arrow next to the clock). Drag the Doomsday Desk icon onto the taskbar to keep it visible.
- **Tray icon click:** opens the desk.
- **Tray icon right-click:** Open Desk, New Note, Quick Capture, Quit.
- **Start with Windows:** available in the tray menu of the *installed* app. It starts silently in the tray, so Ctrl+Alt+N is always ready.
- Only one copy runs at a time. Launching it again brings up the existing window.
- To exit fully, use **Quit** in the tray menu or press **Ctrl+Q**.

---

## Where your notes are stored

Everything lives in one folder:

```
%APPDATA%\Doomsday Desk\
├── notes.json       ← all your notes (plain JSON)
├── notes.json.bak   ← copy from the previous session (automatic safety net)
└── settings.json    ← window positions and small preferences
```

Paste `%APPDATA%\Doomsday Desk` into the File Explorer address bar to open it.

- **Backup:** copy `notes.json` somewhere safe.
- **Move to a new PC:** copy `notes.json` into the same folder there.
- Writes are atomic: the app writes a temp file, then swaps it in, so a crash or power cut can't leave a half-written file. If `notes.json` is ever unreadable, the app loads `notes.json.bak` and keeps the damaged file aside as `notes.corrupt-<time>.json`.
- Uninstalling the app does **not** delete your notes.

---

## Project structure

```
doomsday-desk/
├── main.js            Main process: windows, tray, global shortcut, IPC, security
├── preload.js         Minimal, safe bridge between the pages and main process
├── lib/store.js       Notes + settings persistence (atomic JSON writes)
├── src/
│   ├── index.html     Main desk window
│   ├── app.js         Desk logic: editor, list, search, filters, shortcuts
│   ├── quick.html     Quick capture window
│   ├── quick.js       Quick capture logic
│   └── styles.css     Theme and layout for both windows
├── assets/
│   ├── icon.svg       Original emblem (source)
│   ├── icon.ico       Windows icon (generated, multi-size)
│   └── icon.png       256 px icon (generated)
├── scripts/
│   └── make-icons.js  Rebuilds icon.ico / icon.png from icon.svg
└── package.json       Scripts and electron-builder config
```

### Commands

| Command | What it does |
|---|---|
| `npm start` | Run the app in development |
| `npm run dist` | Build the Windows installer into `dist\` |
| `npm run dist:portable` | Build a single portable `.exe` |
| `npm run icons` | Regenerate `icon.ico` / `icon.png` after editing `assets/icon.svg` |

### Development tips

- **F12** opens DevTools (development only; disabled in the installed app).
- To try things without touching your real notes, point the app at another folder:

  ```powershell
  $env:DESK_DATA_DIR = "C:\temp\desk-test"; npm start
  ```

- Fonts come from Windows itself (Bahnschrift, Segoe UI Variable, Cascadia Mono/Consolas), so nothing is downloaded.

---

## Security

- `contextIsolation`, `sandbox`, and no Node.js in the pages.
- The preload script exposes only a handful of named functions: list, save, and delete notes, open quick capture, and close the window.
- A strict Content-Security-Policy allows only local scripts and styles.
- The main process validates every note it receives (known fields, types, sizes) and accepts messages only from the app's own pages.
- Navigation, new windows, webviews, and permission requests (camera, microphone, and so on) are all blocked.
- No network requests are made.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| **Ctrl+Alt+N does nothing** | Another app has claimed that shortcut. The sidebar shows **Tray** instead of the key; use the sidebar button or tray menu. To pick a different key, change `QUICK_ACCEL` / `QUICK_LABEL` at the top of `main.js`. |
| **The window won't open, but the app is running** | It's in the tray. Click the tray icon (check the **^** hidden icons area). |
| **`npm start` says Electron failed to install** | Run `npm install` again with an internet connection. The runtime downloads on first launch. |
| **SmartScreen warning on the installer** | Expected for unsigned apps: **More info → Run anyway**. |
| **The window opened off-screen** | Delete `settings.json` in `%APPDATA%\Doomsday Desk`. The app also resets the position automatically if a saved one no longer fits any monitor. |
