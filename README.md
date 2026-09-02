# LG NetCast Remote (UDAP 2.0)

A lightweight web and native-wrapped Android application to control 2012-2013 LG Smart TVs running the NetCast 3.0/4.0 OS.

## Features
- Fully functional D-Pad, Volume, and Channel controls.
- Media controls (Play, Pause, Fast Forward, Rewind, Stop).
- System functions (AV Mode, 3D, Input, Info).
- Built-in Key Tester to discover undocumented remote codes.
- Multi-language support (English and Portuguese via auto-detection).

## Architecture & Limitations
LG NetCast TVs require HTTP POST requests to have a strict `User-Agent: UDAP/2.0` and `Content-Type: text/xml`. Standard web browsers block these headers due to CORS policies and forbid modifying the `User-Agent` via JavaScript. 

To solve this, this project offers two ways to run:
1. **Locally via a modified Chrome instance** (for testing/desktop use).
2. **As an Android App via Capacitor** (bypasses web constraints natively).

---

## 1. Running Locally on Desktop (Chrome/Chromium)

To test or use the remote directly from your PC, you must launch your browser with specific flags to disable web security (CORS) and spoof the required User-Agent. 

Make sure you are in the root directory of this project and run the command for your OS:

**For Linux:**
```bash
google-chrome --disable-web-security --user-agent="Mozilla/5.0 (X11; Linux x86_64) UDAP/2.0" --user-data-dir="/tmp/chrome_dev_test" "www/index.html"
```
*(If you use Chromium, replace `google-chrome` with `chromium-browser` or `chromium`)*

**For Windows:**
Open the Command Prompt (Win + R -> `cmd`) and run:
```cmd
"C:\Program Files\Google\Chrome\Application\chrome.exe" --disable-web-security --user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) UDAP/2.0" --user-data-dir="%TMP%\chrome_dev_test" "www\index.html"
```

---

## 2. Building the Android APK (Capacitor)

By wrapping the HTML/JS in Capacitor and enabling the `CapacitorHttp` plugin, the Android app sends raw native HTTP POST requests, permanently solving the CORS and User-Agent issues without needing command-line flags.

This repository uses **GitHub Actions** to automatically build the Android application in the cloud. You do not need Android Studio installed on your computer.

1. Fork or push changes to the `main` branch.
2. Go to the **Actions** tab in this GitHub repository.
3. Click on the **Build Android APK** workflow.
4. Click **Run workflow**.
5. Wait for the build to finish (usually 2-3 minutes).
6. Scroll down to the **Artifacts** section and download the `LG-Remote-App.zip` file.
7. Extract the ZIP file to find the `app-debug.apk` and install it on your Android device.

---

## Note on Touchpad / Magic Remote
The Touchpad feature (Mouse cursor) via network API is not supported on this codebase. While the TV accepts `HandleTouchMove` commands over HTTP POST with a `200 OK`, the internal TV graphics engine requires these coordinates to be sent over a low-latency UDP socket to render the cursor. Browsers cannot handle raw UDP, hence this feature is limited to native socket implementations.
