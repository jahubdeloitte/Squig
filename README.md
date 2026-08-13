# Lanturn — a calm task manager for ADHD brains

A small installable web app. No accounts, no cloud — everything lives in your
phone's local storage.

**What's in here:**
- One task in view at a time, gentle language, no red "overdue" shame — just a
  quiet "+8m over" note.
- A **Board** tab: To start → Doing → Done. Moving a second task into Doing
  gives a soft nudge, not a block.
- A **Focus** tab: a focus-ring Pomodoro timer (25 / 50 / 10 min, or a 5 min
  break), tied to whichever task you're working on.

---

## 1. Try it right now (on this computer)

You need a tiny local server — opening `index.html` directly won't let the
service worker or manifest register properly.

```bash
cd lanturn
python3 -m http.server 8080
```

Then open `http://localhost:8080` in a browser.

## 2. Install it on your Android phone

A PWA needs to be served over **HTTPS** for Android to let you install it
(localhost-only won't reach your phone). The easiest free way:

1. Create a new GitHub repo and push this folder to it:
   ```bash
   cd lanturn
   git init
   git add .
   git commit -m "Lanturn v1"
   git branch -M main
   git remote add origin https://github.com/<you>/lanturn.git
   git push -u origin main
   ```
2. On GitHub: **Settings → Pages → Deploy from branch → main → / (root)**.
   GitHub gives you a URL like `https://<you>.github.io/lanturn/`.
3. Open that URL on your Android phone in Chrome → menu (⋮) → **Add to Home
   screen** / **Install app**. It now behaves like a normal app: its own
   icon, no browser bar, works offline.

## 3. Turning this into a real `.apk` later

Once you have Android Studio installed, you don't need to rebuild anything —
you wrap this same web code in a thin native shell:

**Easiest: Capacitor** (keeps this exact HTML/CSS/JS, adds a native project
around it)
```bash
npm install @capacitor/core @capacitor/cli @capacitor/android
npx cap init lanturn com.yourname.lanturn
npx cap add android
# copy index.html, manifest.json, sw.js, icons/ into ./www
npx cap sync
npx cap open android   # opens Android Studio, hit Run or Build > Generate APK
```

**Alternative: plain WebView wrapper** — a one-`Activity` Kotlin app that
loads `index.html` in a `WebView`. More manual, but zero extra dependencies.
Ask me for this project scaffold whenever you're ready and I'll generate it.

Either way: your task data, timer logic, and design all carry over untouched.
The only things you'd add at that stage are native-only extras, like a
background notification that survives the app being closed, or a home-screen
widget — Android APIs that a website can't reach.

---

## Notes on the design choices

- **Dark, low-glare palette** (deep indigo, warm coral accent) — meant to be
  calm rather than alerting, since a wall of red badges reads as pressure.
- **Doing column isn't hard-capped** — the nudge is a suggestion, not a
  lock, because forcing behavior tends to backfire; noticing is often enough.
- **No push notifications for reminders yet** — those need real permission
  handling and a backend/service worker push setup, which is a good v2
  addition once this is running on a server you control.
