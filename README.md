# Squig — a calm task manager for ADHD brains

A small installable web app. No accounts, no cloud — everything lives in your
phone's local storage.

> Formerly "Lanturn" — renamed to Squig. If you had it installed under the
> old name, your tasks and sessions carry over automatically the first time
> you open the new version (see `migrateFromLanturn` in `index.html`).

**What's in here:**
- **Four tabs, color-coded**: **Daily** (green), **Weekly** (blue), and
  **Monthly** (purple) each hold their own recurring tasks — swipe one done
  and it vanishes until its next occurrence is actually due, no manual
  re-adding. **Focus** (amber) is where one-off, non-repeating tasks live,
  alongside a Pomodoro-style timer for sitting down and grinding through one.
- **The tab you add from decides the type.** Tap + on the Daily tab and
  you're adding a daily task; no dropdown to fumble with. Repeat type is
  fixed at creation — to change a task's type, delete and re-add it from
  the right tab.
- **Swipe to complete**, any tab: swipe a card either direction to mark it
  done. Tap it instead to edit its due date/reminder, jump into a focus
  session, or delete it.
- **Due dates & reminders**: Daily/Weekly/Monthly tasks require a due
  date/time since that's what anchors the repeat schedule. One-off (Focus)
  tasks can have one but don't have to.
- **Focus tab's Timer** sub-view is the original Pomodoro ring (25 / 50 / 10
  min, or a 5 min break) — tap "Start focus session" on any task, from any
  tab, to attach it and jump straight there.

---

## 1. Try it right now (on this computer)

You need a tiny local server — opening `index.html` directly won't let the
service worker or manifest register properly.

```bash
cd squig
python3 -m http.server 8080
```

Then open `http://localhost:8080` in a browser.

## 2. Install it on your Android phone

A PWA needs to be served over **HTTPS** for Android to let you install it
(localhost-only won't reach your phone). The easiest free way:

1. Create a new GitHub repo and push this folder to it:
   ```bash
   cd squig
   git init
   git add .
   git commit -m "Squig v1"
   git branch -M main
   git remote add origin https://github.com/<you>/squig.git
   git push -u origin main
   ```
2. On GitHub: **Settings → Pages → Deploy from branch → main → / (root)**.
   GitHub gives you a URL like `https://<you>.github.io/squig/`.
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
npx cap init squig com.yourname.squig
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
- **No Doing/Done columns** — earlier versions had a three-column board and
  then a single sorted list; splitting by cadence (Daily/Weekly/Monthly/
  one-off) instead makes each tab small and specific rather than one long
  mixed list.
- **Repeat type is locked at creation, not editable.** Simpler data model,
  and it matches how these get used in practice — a "take meds" task isn't
  going to switch from daily to monthly later.
- **Reminders are local, not push-based.** Squig asks for notification
  permission the first time you set a due date, then checks every 20s while
  the app/tab is open (including backgrounded) and fires a real system
  notification via the Notification API. What this **won't** do: wake up
  and notify you if you've fully force-closed the app or restarted your
  phone — that needs server-sent Push (a backend that can wake the service
  worker even when nothing is open), which is a good v2 addition once
  this is running on a server you control rather than as a local PWA.
