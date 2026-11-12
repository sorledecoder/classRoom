# Voice & Video Recorder — Offline Web App version

This is a browser-based (PWA) version of the recorder. It can be
**installed as an app icon** and used **offline**. It uses standard
web APIs, so microphone/webcam access needs your explicit permission
each time Chrome/Edge asks (that part can't be skipped — it's a
browser security requirement, not something an app setting can turn off).

## Two honest limits vs. the native Windows version

- **Lock-screen reliability**: browsers suspend/throttle background
  tabs much more aggressively than a native app. Don't rely on this
  for long unattended recordings across a locked screen the way you
  could with the native .exe version.
- **Mouse tracking**: a web page can only see mouse movement over
  itself — it cannot read your cursor position outside the browser,
  **even if you pick "Entire screen"** in the share dialog; this is a
  browser privacy boundary, not a setting. The "Screen & Mouse" tab
  instead records your **whole screen as video**, which naturally shows
  the cursor moving and clicking in the video itself, but it can't
  produce a separate CSV log of system-wide coordinates like the
  native version can.

### Microphone narration — "Entire screen" only (outside Tutorial mode)

When you pick **"Entire screen"** in the share dialog, your microphone
is captured and mixed into the recording's audio track, so you can
narrate while you demo. If you instead pick **"Window"** or
**"Microsoft Edge Tab"**, the recording stays video-only, same as
before — no mic is requested for those two, *unless Tutorial mode is on*
(see below, which always captures your mic regardless of what you share).

### Capturing sound already playing on your screen

Separately from your mic, the app now also tries to capture whatever
sound is already playing on the screen you're sharing (a video, music,
a notification) — this is different from narration and works for any
share type. Whether it actually works depends on your browser's own
share dialog: Chrome/Edge show a **"Share audio" / "Also share system
audio"** checkbox when you pick Entire Screen, a Window, or a Chrome/Edge
Tab — tick it there. This app can't turn that on for you; it's a
browser-level permission, same as the mic/camera prompts.

### Tutorial mode (webcam overlay + voice, like Loom/Camtasia)

Tick **"Tutorial mode"** before starting a screen recording to get:

- Your **webcam** composited into the recording as a circular picture-
  in-picture overlay, in whichever corner you pick from the dropdown
  (bottom-left, bottom-right, top-left, top-right).
- Your **microphone** always captured and mixed in, regardless of
  whether you share the entire screen, a window, or a tab.
- Any **sound already playing on your screen**, mixed in alongside
  your narration (see above — still depends on ticking "Share audio"
  in the browser's own dialog).
- A **3-2-1 countdown** before recording starts, and a live **elapsed-
  time counter** while recording — the same touches you'd expect from
  a dedicated screen-recording app.
- A **live composited preview** (screen + your webcam bubble) shown in
  the app while you record, so you can see exactly what's being
  captured in real time.

This needs both screen-share and webcam/mic permissions, so expect two
separate browser prompts when you start. If the webcam/mic prompt is
denied, it falls back to recording the screen only (no overlay).

Tutorial recordings are saved and listed the same way as regular screen
recordings, with Pause/Resume/Stop all working the same.

## Step 1 — Run a local server (needed once, for install + offline caching)

Browsers only allow microphone/camera access and app installation
over `https://` or `http://localhost` — not from a file opened directly.
So you need a tiny local server. If you have Python installed (see the
native app's README for how to install it), this is one click:

1. Unzip this folder somewhere, e.g. `C:\Users\<you>\VoiceVideoRecorderWeb`.
2. Double-click **`start_server.bat`**.
3. It opens `http://localhost:8000/index.html` in your default browser automatically.

(No Python? You can instead use VS Code's "Live Server" extension, or
run `npx serve` if you have Node.js installed — same idea, just needs
something serving the folder over `http://localhost`.)

## Step 2 — Allow permissions

The browser will ask for microphone access (Voice tab) and camera
access (Webcam tab) the first time you use each — click **Allow**.

## Step 3 — Install it as an app

In Chrome or Edge, look for an **install icon** (⊕ or a small monitor
icon) in the address bar, or open the browser's **⋮ menu → Install
Voice & Video Recorder**. This adds it to your Start menu/taskbar like
a normal app, in its own window with no browser tabs/address bar.

## Step 4 — Using it offline afterwards

Once installed, the app's files (HTML/CSS/JS/icons) are cached by a
service worker, so the app interface itself loads with **no internet
connection needed**. Recording and playback don't need the internet
either — they're all local, on-device.

One practical note: because it was installed from `http://localhost:8000`,
re-launching it may try to reach that address first. Keeping
`start_server.bat` running in the background (or adding a shortcut to
it in your Windows **Startup folder**, `shell:startup`) means the app
is always ready to open — after the very first successful load, the
service worker will keep the app usable even if that local server
isn't running.

## Where recordings go

Every recording is saved **on your device** in the browser's local
storage (IndexedDB) as soon as you stop recording, so it's still there
the next time you open the app — even after closing the browser or
restarting your PC. Each recording also has a **⬇ Download** button to
save it as a `.webm` file to your Downloads folder if you want a copy
outside the app, and a **🗑 Delete** button to remove it for good.

Two things worth knowing:
- This storage is tied to *this specific browser on this specific PC* —
  it won't show up if you open the app in a different browser, a
  different Windows user account, or clear your browsing data.
- Video recordings can be large; if you record a lot, keep an eye on
  storage and delete ones you don't need. Unlike the native version,
  there's no separate folder to browse in File Explorer — everything
  lives inside the app itself.

## If you'd rather have the native app instead

The earlier native Windows version (`VoiceVideoRecorder.zip`) auto-saves
every recording to a folder, keeps running more reliably through a
locked screen, and can log real mouse coordinates anywhere on the
screen — things a browser sandbox won't allow. Use whichever fits your
actual use case better; you now have both.
