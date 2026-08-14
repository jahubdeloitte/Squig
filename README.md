# Squig — a calm task manager for ADHD brains

A small installable web app. No accounts, no cloud — everything lives in your
phone's local storage.

> Formerly "Lanturn" — renamed to Squig. If you had it installed under the
> old name, your tasks and sessions carry over automatically the first time
> you open the new version (see `migrateFromLanturn` in `index.html`).

**What's in here:**
- A single **Tasks** list, sorted by what's due soonest. No columns to
  drag things through — just add a task and swipe it away when it's done.
- **Swipe to complete**: swipe a task card either direction to mark it
  done. Tap a card instead to edit it, change its due date/reminder, or
  jump into a focus session on it.
- **Due dates & reminders**: give any task a due date/time (when adding it,
  or later by tapping it) and Squig will nudge you when it hits — an amber
  badge in the last hour, a notification when it's due.
- **Repeating tasks**: set a task to repeat Daily / Weekly / Monthly (needs
  a due date to anchor to). Swipe a repeating task done and Squig
  automatically queues the next occurrence at the next interval — so
  "take meds", "water plants", or "submit timesheet" never need re-adding.
- A **Focus** tab: a focus-ring Pomodoro timer (25 / 50 / 10 min, or a 5 min
  break) for one-off tasks you want to sit down and grind through — tap
  "Start focus session" from any task's detail view to attach it.

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
- **No Doing/Done columns** — earlier versions had a three-column board, but
  for a reminders-first app a single sorted list with a quick swipe-to-clear
  gesture is less to manage and matches how the app is actually used.
- **Reminders are local, not push-based.** Squig asks for notification
  permission the first time you set a due date, then checks every 20s while
  the app/tab is open (including backgrounded) and fires a real system
  notification via the Notification API. What this **won't** do: wake up
  and notify you if you've fully force-closed the app or restarted your
  phone — that needs server-sent Push (a backend that can wake the service
  worker even when nothing is open), which is a good v2 addition once
  this is running on a server you control rather than as a local PWA.
