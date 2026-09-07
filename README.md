# LG NetCast Remote (UDAP 2.0)

A lightweight web and native-wrapped Android application to control 2012-2013 LG Smart TVs running the NetCast 3.0/4.0 OS via the UDAP 2.0 protocol.

## Features
- **Smart Connection Manager:** Saves paired TV credentials, displays real-time connection status (*Connected* / *Out of reach*), and auto-reconnects on startup.
- **Optimistic UI:** Instant volume display update (`VOL +`, `VOL -`, `MUTE`) with background polling sync.
- **Live Typing & Buffer:** Translates physical keyboard input into full-word text injection (`TextEdited`), automatically clearing the buffer on D-Pad navigation or input loss.
- **Touchpad & Wheel Navigation:** Touchpad support (via `CursorVisible` + `HandleTouchMove` / `HandleTouchClick`), two-finger scrolling, and desktop mouse wheel support.
- **Screencast & Theater Mode:** Real-time TV screen frame capture with expandable Theater Mode and integrated touch controls.
- **Haptic Feedback:** Physical vibration feedback for buttons, touchpad clicks, and scroll increments (`navigator.vibrate`).
- **Control & Utility Mapping:** Full D-Pad, Media controls, Color keys, PageUp/PageDown (Channel shortcuts), and `Esc` for `BACK`.
- **Built-in Key Tester:** Integrated tool at the bottom of the interface to discover undocumented key codes (strictly bounded to positive ranges).
- **Multi-language Support:** Automatic English and Portuguese interface detection.

## Architecture & Limitations
LG NetCast TVs require HTTP POST requests with a strict `User-Agent: UDAP/2.0` and `Content-Type: text/xml`. Standard web browsers block these headers due to CORS policies and forbid modifying the `User-Agent` via JavaScript.

To bypass this restriction, the project supports two execution modes:
1. **Locally via a modified Chrome instance** (for PC/desktop testing).
2. **As an Android App via Capacitor** (uses native HTTP plugins, bypassing CORS and browser header locks).

---

## 1. Running Locally on Desktop (Chrome/Chromium)

To test or use the remote directly from your PC, launch your browser with flags to disable web security (CORS) and spoof the required `User-Agent`.

Make sure you are in the root directory of this project and run:

**For Linux:**
    google-chrome --disable-web-security --user-agent="Mozilla/5.0 (X11; Linux x86_64) UDAP/2.0" --user-data-dir="/tmp/chrome_dev_test" "www/index.html"

*(If you use Chromium, replace `google-chrome` with `chromium-browser` or `chromium`)*

**For Windows:**
Open the Command Prompt (Win + R -> `cmd`) and run:
    "C:\Program Files\Google\Chrome\Application\chrome.exe" --disable-web-security --user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) UDAP/2.0" --user-data-dir="%TMP%\chrome_dev_test" "www\index.html"

---

## 2. Building the Android APK & Assets (GitHub Actions)

By wrapping the web app in Capacitor and enabling `CapacitorHttp`, the Android app fires raw native HTTP requests, permanently resolving CORS and `User-Agent` restrictions.

This repository utilizes **GitHub Actions** to generate app icons and compile the Android APK in the cloud without requiring local Android Studio installations.

### Setting Up App Icons
1. Place a square image (1024x1024 px recommended) inside the `assets/` directory at the project root as `icon.jpg` (or `icon.png`).
2. Ensure your GitHub Workflow `.yml` contains the asset generator step before `npx cap sync`:

      - name: Generate App Assets & Icons
        run: |
          npm install @capacitor/assets --no-save
          npx capacitor-assets generate

### Triggering the Build
1. Push changes or trigger the workflow under the **Actions** tab in GitHub.
2. Select **Build Android APK** -> **Run workflow**.
3. Once completed (2-3 minutes), download `LG-Remote-App.zip` under **Artifacts**.
4. Extract the ZIP to find `app-debug.apk` and install it on your device.

---

## Technical Notes & Protocol Insights
- **Touchpad Mechanism:** NetCast OS ignores touch commands (`HandleTouchMove` / `HandleTouchClick`) unless the Magic Remote cursor is explicitly awakened first via a `CursorVisible` event (`<value>true</value>`).
- **Focus Drop Strategy:** Clearing focus from native TV text fields without closing active apps is achieved by triggering a cursor wake followed by an out-of-bounds negative relative move (`X: -500, Y: -500`) and a click at screen margins.
