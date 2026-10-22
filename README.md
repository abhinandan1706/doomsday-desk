# Doomsday Desk

**Version 1.0.0** · Windows · Electron 44

A small, fast notes app for Windows with a dark, cinematic look: black and charcoal panels, crimson accents, and an original "doomsday clock" emblem. It opens on a blank note with the cursor ready, and there's no Save button because every keystroke saves itself.

It's built with Electron and plain HTML/CSS/JavaScript. There's no backend, no account, no network access, and no tracking. Your notes live in one JSON file on your PC.

---

## Install

The latest build is already in the `dist` folder:

| File | What it is |
|---|---|
| **`dist\Doomsday Desk Setup 1.0.0.exe`** | The installer (~107 MB). Run it to install with Start menu and desktop shortcuts. You can choose the install folder. |
| `dist\win-unpacked\Doomsday Desk.exe` | The app itself, unpacked. Runs directly without installing. Keep the whole `win-unpacked` folder together. |

> The build isn't code-signed, so Windows SmartScreen may show **"Windows protected your PC"** the first time. Click **More info → Run anyway**. That's normal for personal, unsigned apps.

To rebuild these files from source, see [Building](#building).

---

## Features

| | |
|---|---|
| **Instant start** | Opens on a fresh note with the cursor ready. Just type. |
| **Auto-save** | Edits reach the disk within about a third of a second. The title bar shows *Saving* / *Saved*, or *Save failed* (it keeps retrying) if the disk can't be written. Closing, switching notes, quitting, or Windows shutting down never loses text. |
| **Multiple notes** | Create, edit, delete, pin, and switch between notes. A note with no title uses its first line as its name. |
| **Search** | Searches titles and note text as you type and highlights matches. Several words narrow the results (all must match). |
| **Categories** | Four optional colour tags: Ideas, Study, Code, Reminders. **Code** notes switch to a monospace font and turn spell-check off. A new note made while a category is selected gets that category. |
| **Pinning** | Pinned notes stay at the top of the list in their own section. |
| **Quick capture** | A small always-on-top window you can open from any app with **Ctrl+Alt+N**. Type a thought and press Esc; it's already filed in your desk. |
| **System tray** | Closing the window keeps the app in the tray, so quick capture still works. Click the tray icon to reopen it. |
| **Undo delete** | Deleting a note shows an **Undo** button for 7 seconds. |
| **Window memory** | Both windows reopen where you left them, at the same size (and maximised, if it was). |
| **Adjustable text** | **Ctrl + = / − / 0** changes the editor font size (12–26 px) and remembers it. |
| **Right-click menu** | Undo, redo, cut, copy, paste, select all, plus spelling suggestions and *Add to dictionary*. |
| **Tidy by default** | Empty notes are discarded automatically, so blank notes don't pile up. |
| **Reduced motion** | Animations turn off when Windows' "Animation effects" setting is off. |

### Layout

```
┌───────────┬──────────────┬──────────────────────────────┐
│ New note  │ Search       │ ○ ● ● ● ●            📌  🗑   │
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
│ Shortcuts │              │                              │
└───────────┴──────────────┴──────────────────────────────┘
  sidebar     note list      editor
```

On narrow windows the sidebar shrinks to an icon rail.

---

## Keyboard shortcuts

Press **Ctrl + /** in the app to see these at any time.

### Main window

| Action | Shortcut |
|---|---|
| New note | **Ctrl + N** |
| Search notes | **Ctrl + F** |
| Previous / next note | **Alt + ↑ / ↓** |
| Pin / unpin | **Ctrl + P** |
| Delete note (undoable) | **Ctrl + Shift + Del** (or Backspace) |
| Quick capture (works from any app) | **Ctrl + Alt + N** |
| Text size bigger / smaller / reset | **Ctrl + = / − / 0** |
| Show shortcuts | **Ctrl + /** |
| Close window (stays in tray) | **Ctrl + W** |
| Quit completely | **Ctrl + Q** |

- **In the search box:** **↑ / ↓** move through results, **Enter** opens one, **Esc** clears the search, and a second **Esc** returns to the editor.
- **In the title field:** **Enter** or **↓** jumps to the note body.
- **In the note body:** **Tab** inserts a tab character, which is handy for code.

### Quick capture window

| Action | Shortcut |
|---|---|
| Done (close; it's already saved) | **Esc**, **Ctrl + Enter**, or **Ctrl + W** |
| Start another note | **Ctrl + N** |
| Open this note in the full desk | **Ctrl + O** |
| Open / close from anywhere | **Ctrl + Alt + N** |

Each time quick capture opens, it starts a new note. Quick notes have no category and show up under **All notes**.

---

## Tray and background behaviour

- Clicking **X** or pressing **Ctrl+W** hides the window to the system tray. The first time you do this, a notification tells you so.
- On Windows 11, new tray icons start in the **hidden icons** area (the **^** arrow next to the clock). Drag the Doomsday Desk icon onto the taskbar to keep it visible.
- **Tray icon click:** opens the desk.
- **Tray icon right-click:** Open Desk, New Note, Quick Capture, Quit.
- **Start with Windows:** a checkbox in the tray menu of the *installed* app (not shown when running from source). It starts hidden in the tray, so Ctrl+Alt+N is always ready.
- Only one copy runs at a time. Launching it again brings up the existing window.
- To exit fully, use **Quit Doomsday Desk** in the tray menu or press **Ctrl+Q**.

---

## Where your notes are stored

Everything lives in one folder:

```
%APPDATA%\Doomsday Desk\
├── notes.json       ← all your notes (plain JSON)
├── notes.json.bak   ← copy from the previous session (automatic safety net)
└── settings.json    ← window positions and one-time hints
```

Paste `%APPDATA%\Doomsday Desk` into the File Explorer address bar to open it.

- **Backup:** copy `notes.json` somewhere safe.
- **Move to a new PC:** copy `notes.json` into the same folder there.
- **Crash-safe writes:** the app writes a temp file and then swaps it in, so a crash or power cut can't leave a half-written file. If `notes.json` is ever unreadable, the app loads `notes.json.bak` and moves the damaged file aside as `notes.corrupt-<time>.json`.
- Uninstalling the app does **not** delete your notes.

---

## Building

You need [Node.js](https://nodejs.org) 18 or newer. The current build was made with Node 24.16, npm 12.2, Electron 44.9.1, and electron-builder 26.15.3.

```bash
npm install
```

Run from source:

```bash
npm start
```

The first `npm install` downloads the Electron runtime (about 100 MB), so it takes a minute.

Build the Windows installer into `dist\`:

```bash
npm run dist
```

Build a single portable `.exe` that runs without installing (`dist\DoomsdayDesk-1.0.0-portable.exe`):

```bash
npm run dist:portable
```

### Releasing a new version

1. Bump `"version"` in `package.json` (for example to `1.1.0`).
2. Run `npm run dist`. The installer is named after the version, for example `dist\Doomsday Desk Setup 1.1.0.exe`.
3. Update the version line at the top of this README.

Installing a newer version over an older one keeps your notes, since they live in `%APPDATA%`, not in the install folder.

### Commands

| Command | What it does |
|---|---|
| `npm start` | Run the app from source |
| `npm run dist` | Build the Windows installer (NSIS) into `dist\` |
| `npm run dist:portable` | Build a single portable `.exe` into `dist\` |
| `npm run icons` | Regenerate `icon.ico` / `icon.png` after editing `assets/icon.svg` |

### Development tips

- **F12** opens DevTools when running from source. It's disabled in the installed app.
- To experiment without touching your real notes, point the app at another folder:

  ```powershell
  $env:DESK_DATA_DIR = "C:\temp\desk-test"; npm start
  ```

- Fonts come from Windows itself (Bahnschrift, Segoe UI Variable, Cascadia Mono/Consolas), so nothing is downloaded.
- To change the quick-capture hotkey, edit `QUICK_ACCEL` and `QUICK_LABEL` at the top of `main.js`.

---

## How it works

The **main process** (`main.js`) owns the notes. It holds them in memory through `NoteStore` (`lib/store.js`), which debounces writes to `notes.json`. It also creates the two windows, the tray icon, and the global shortcut.

The **pages** (`src/`) are sandboxed and have no Node.js access. They talk to the main process only through the small `window.desk` API defined in `preload.js`:

| Call | Purpose |
|---|---|
| `listNotes()` / `saveNote(note)` / `deleteNote(id)` | Read and write notes |
| `onNotesChanged(cb)` | Get edits made in the other window, so the desk and quick capture stay in sync |
| `onStatus(cb)` | Saving / saved / error status for the title bar |
| `onCommand(cb)` | Commands from the tray or main process (new note, focus a note) |
| `openQuick()` / `openInDesk(id)` / `closeWindow()` / `quit()` / `info()` | Window control and app info |

Every edit in the page is sent straight to the main process, so there's never unsaved text sitting in a window.

### Project structure

```
notess/
├── main.js            Main process: windows, tray, global shortcut, IPC, security
├── preload.js         The window.desk bridge between pages and main process
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
├── dist/              Build output (installer + win-unpacked app)
└── package.json       Scripts and electron-builder config
```

---

## Security

- `contextIsolation`, `sandbox`, and no Node.js in the pages.
- The preload script exposes only the named functions listed above.
- A strict Content-Security-Policy allows only local scripts and styles.
- The main process validates every note it receives (known fields, types, sizes) and accepts messages only from the app's own `file://` pages.
- Navigation, new windows, webviews, and permission requests (camera, microphone, and so on) are all blocked.
- The app makes no network requests.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| **Ctrl+Alt+N does nothing** | Another app has claimed that shortcut. The sidebar shows **Tray** instead of the key; use the sidebar button or the tray menu, or change the hotkey in `main.js` (see [Development tips](#development-tips)). |
| **The window won't open, but the app is running** | It's in the tray. Click the tray icon (check the **^** hidden icons area). |
| **No "Start with Windows" option in the tray** | That option only appears in the installed app, not when running with `npm start`. |
| **`npm start` says Electron failed to install** | Run `npm install` again with an internet connection. |
| **SmartScreen warning on the installer** | Expected for unsigned apps: **More info → Run anyway**. |
| **The window opened off-screen** | Delete `settings.json` in `%APPDATA%\Doomsday Desk`. The app also resets the position automatically if a saved one no longer fits any monitor. |
| **Title bar says "Save failed"** | The notes folder couldn't be written (disk full, permissions, or a locked file). Your text is still in memory and the app retries every 2 seconds. Hover the status for the error. |
