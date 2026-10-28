# TikTok Unliker

An Android app for removing likes from your own TikTok account in bulk. Log in to TikTok inside the app, switch on Select mode, tap all the liked videos you want to get rid of (without opening them), then press **Unlike**. The app removes the likes for you, one video at a time.

> **Unofficial.** Not affiliated with, endorsed by, or connected to TikTok or ByteDance. "TikTok" is a trademark of its respective owner. Personal-use tool that works inside your own logged-in session.

---

## Features

- **Built-in TikTok browser.** Log in to your own account like normal.
- **Select mode.** Tapping a video selects it instead of opening it. Selected videos get a red outline and a check mark. Tap again to deselect.
- **Bulk unlike.** Removes the like from every selected video automatically.
- **PC mode on/off switch** (on by default). Uses the desktop site, so search and the Liked tab work properly and the "Open TikTok app" popup disappears.
- **Safe by design.** It only presses the heart when the video is confirmed as liked, so it can never add a like by accident.
- **Human-like pace.** A few seconds' pause between videos, and an automatic stop after 3 failures in a row (for example a captcha).
- **Progress and summary.** Shows how far it is and finishes with a report of unliked, already-not-liked and failed videos. Failed ones stay selected so you can retry.
- **Stop any time** with the same button.

---

## How to use

The bottom bar has five buttons:

| Button | What it does |
|---|---|
| **Select: OFF / ON** | Selection mode. While ON, tapping a video selects it instead of opening it. |
| **+ Video** | Adds the video you currently have open. |
| **Unlike (n)** | Removes the like from all *n* selected videos (asks to confirm). Turns into **Stop** while running. |
| **Clear** | Empties the selection. |
| **PC: ON / OFF** | Switches between the desktop and mobile versions of TikTok. |

### Typical workflow

1. Open the app and log in to TikTok.
2. Open your profile and the **Liked** tab.
3. Turn **Select** ON and tap the videos you want to unlike.
4. Tap **Unlike (n)** and confirm.
5. Keep the app open until the summary appears. The Liked list reloads afterwards.

### Tips

- Try it with 1-2 videos first, then do bigger batches.
- Keep the app open and the screen on while it runs.
- Do a few dozen videos at a time. Many actions in a short time can trigger captchas or temporary limits from TikTok.
- If it stops after 3 failures, tap **View page** in the dialog to check for a captcha or login prompt.
- The desktop page looks small on a phone. Pinch to zoom.

---

## How it works

TikTok only offers the heart button on a video's own page, so the app works through the selection as a queue.

1. **Selecting.** In the main browser, an injected script (`inject.js`) catches taps on video thumbnails in Select mode and reports their links to the app through a JavaScript bridge.
2. **Background browser.** When you press **Unlike**, a second, hidden desktop browser opens each selected video in turn. It shares the same login as the main browser.
3. **Unliking.** A script (`unlike.js`) waits for the heart button, reads whether the video is liked (`aria-pressed`, with a colour check as fallback), clicks it only if it is liked, then confirms that the state changed.
4. **Queue.** The result is sent back to the app, the video leaves the selection, and the next one starts after a short random pause.
5. **Safety stop.** After 3 consecutive failures the run stops and offers to show the page, so you can solve a captcha or log in again.

---

## Privacy

- No analytics, no tracking, no ads and no accounts of its own.
- The app has no server and sends nothing anywhere. It only talks to TikTok's website, like a browser would.
- The only permission it requests is `INTERNET`.
- Your TikTok login is handled by TikTok's own page inside the in-app browser.

---

## Install

### Build with GitHub Actions (no programming needed)

1. Upload `TikTokUnliker.zip` to this repository, together with the workflow file `.github/workflows/build.yml`.
2. Open the **Actions** tab, select **Build APK** and wait for the green check. Pushing a commit starts a build, and **Run workflow** starts one by hand.
3. Open the finished run and download the **TikTokUnliker-apk** artifact.
4. Unzip it to get `app-debug.apk`, copy it to your phone and open it.
5. Allow **Install unknown apps** for your browser or file manager when Android asks.

### Build with Android Studio

1. Unzip `TikTokUnliker.zip` and open the `TikTokUnliker` folder in Android Studio.
2. Let Gradle sync.
3. Choose **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
4. The APK is at `app/build/outputs/apk/debug/app-debug.apk`.

---

## Requirements

- Android 7.0 (API 24) or newer
- An up-to-date *Android System WebView* / Chrome
- Internet connection and a TikTok account

---

## Technical details

| | |
|---|---|
| Language | Java (no third-party libraries) |
| UI | Programmatic layout: two `WebView`s plus a button bar |
| Page logic | Plain JavaScript in `assets/inject.js` (selecting) and `assets/unlike.js` (unliking) |
| Build system | Gradle with Android Gradle Plugin 8.5.2 |
| Min / target SDK | 24 / 34 |
| CI | GitHub Actions (JDK 17, Gradle 8.9) |

```
TikTokUnliker/
├── .github/workflows/build.yml
├── build.gradle, settings.gradle
└── app/
    ├── build.gradle
    └── src/main/
        ├── AndroidManifest.xml
        ├── java/com/example/tiktokunliker/MainActivity.java
        ├── assets/inject.js       Selection logic for the main browser
        ├── assets/unlike.js       Removes the like
