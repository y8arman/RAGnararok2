# ⚡ RAGnarok — A Wavestone AI Showdown

A neon **Tron-meets-Norse** real-time multiplayer quiz game — Kahoot-style — built as a
**single self-contained `index.html`**. No build step, no framework, no dependencies to
install. One host drives the projector; warriors join from their phones. Live sync runs on
**Firebase Realtime Database**.

## ✨ Features

- **Host + many players** in real time — host screen on a projector, players on their phones.
- **Join by 4-char code or QR** (the QR encodes the page URL + `?join=CODE`).
- **Emoji sigil + nickname** picker with a length cap and profanity filter.
- **Host-driven game loop:** lobby → `3-2-1 countdown` → question → reveal → animated
  leaderboard → Valhalla podium.
- **Speed-based, host-authoritative scoring** with streak bonuses.
- **5 realms that re-skin the board** each question (Niflheim, Muspelheim, The Void, Bifröst,
  Asgard) via colour-cycling CSS variables.
- **Full show polish:** animated Tron grid, neon-flicker boot logo, count-up leaderboard with
  climb badges, a Norse commentary engine, Web Audio synth SFX (no audio files), phone haptics,
  green/red reveal flash, and a sequenced podium with drumroll, confetti, and fanfare.

## 🚀 Quick start

1. Create a Firebase project and a **Realtime Database** (not Firestore).
2. Open `index.html` and paste your web config into the clearly marked `firebaseConfig` block
   near the top. `databaseURL` is **required**.
3. Host it anywhere static (GitHub Pages, Netlify Drop, any web server) or open it locally.
4. Click **Host Realm** on the big screen; players scan the QR or enter the code.

> The app uses the Firebase **compat SDK** loaded from the gstatic CDN, so it runs from a plain
> file with no bundler.

## 🎮 How to play

- **Host:** open the page → **Host Realm** → share the code/QR → **Begin the Saga** → advance
  through the phases with the on-screen buttons.
- **Players:** open the page (or scan the QR) → **Join Battle** → pick a sigil + name → tap
  answers before the timer runs out.

## 🧠 Editing the quiz

Questions live in the `QUESTIONS` array at the top of `index.html`:

```js
{ q:"Question text",
  options:["A","B","C","D"],  // 2 options renders as True / False
  answer:0,                    // index of the correct option
  time:20000 }                 // milliseconds
```

There are **5 questions**, one per realm. Keep it at 5 to match the realm cycle, or adjust to
taste — the realm skin uses `questionIndex % 5`.

## 🏆 Scoring

Computed by the host on reveal:

- Base **1000 points**, scaled by speed: instant answer ≈ 1000, last-second ≈ 500
  (`round-to-10 of 1000 × (1 − frac × 0.5)`, where `frac` is how far into the timer you answered).
- **+100 per streak** beyond the first correct, capped at 5.
- Wrong or no answer = **0**, and your streak resets.

## 🛠️ Tech

- Vanilla HTML/CSS/JS, single file.
- **Firebase Realtime Database** (compat SDK, gstatic CDN) for all live sync.
- **qrcodejs** (cdnjs) for the join QR.
- **Web Audio API** for all sound; **Vibration API** for haptics; **Canvas** for the grid,
  leaderboard bars, and confetti.
- Fonts: **Orbitron** (titles) + **Space Grotesk** (body) via Google Fonts.

Game state lives under `games/{code}`: `phase`, `currentQ`, `questionStartAt` (server
timestamp), `players/{id}` (name, avatar, score, streak, lastPoints, lastCorrect), and
`answers/{qIndex}/{playerId}`. Disconnected players are removed automatically via `onDisconnect`.

## 🔒 Security notes

- The Firebase `apiKey` in a web app is **not a secret** — it identifies the project, it doesn't
  grant access. Your **Realtime Database security rules** are what protect the data, so tighten
  them before any real event and don't leave the database in open test mode.
- This is an MVP: correct-answer indices live client-side in `QUESTIONS`, so a technically savvy
  player could read them from the page source. Fine for a fun internal showdown; for a
  tamper-proof version, move answer-checking and scoring into a Cloud Function.

## 📄 License

MIT — do what you like. Attribution appreciated but not required.
