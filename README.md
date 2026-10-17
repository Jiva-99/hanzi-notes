# Hanzi Notes 汉字

A personal Chinese study notebook. Record what you learn in each tutoring session (vocab, texts, grammar, exercises) and review it with spaced repetition. Pinyin is generated automatically and colour-coded by tone.

Single-file app (`index.html`), hosted on GitHub Pages, synced with Firebase (Google sign-in + Firestore). Works offline and can be added to the iPhone/iPad Home Screen.

## Features

- **Today**: cards due, 7-day forecast, streak, quick add with live pinyin, last lesson, open mistakes and questions for your tutor
- **Lessons**: one per tutoring session; everything you add can be tagged to a lesson
- **Vocabulary**: auto pinyin (editable), meaning, type, measure word, example sentence, status (New / Learning / Known), bulk paste
- **Texts**: pinyin above every character, translation / pinyin / tone-colour toggles, tap a character to look it up or add it, star sentences to memorise, read aloud
- **Grammar**: pattern formula, explanation, examples with pinyin, common-mistake box
- **Exercises**: log answers; wrong ones become your mistakes list until resolved
- **Review**: spaced repetition (SM-2 style), Hanzi → English or English → Hanzi, keyboard: `Space` flip, `1–4` rate
- Search across everything (characters, pinyin with or without tones, English), dark mode, JSON export/import

## Setup

### 1. Firebase (once)

1. Go to [console.firebase.google.com](https://console.firebase.google.com) and create a project (or reuse an existing one — data lives under its own `hanzi/` collection).
2. **Build → Authentication → Get started → Google** → enable.
3. **Build → Firestore Database → Create database** (production mode).
4. **Firestore → Rules**: paste the contents of `firestore.rules` and publish. If you reuse a project that already has rules, add the `match /hanzi/{uid}/{document=**}` block inside the existing `match /databases/{database}/documents { … }`.
5. **Project settings → General → Your apps → Web app (</>)**: register an app and copy the `firebaseConfig` object.
6. Open `index.html`, find `const firebaseConfig = {` near the top of the script, and paste your values.

Until you paste a config, the app runs in **local mode** (data saved in that browser only), so you can try it straight away.

### 2. GitHub Pages

1. Push this folder to a GitHub repo.
2. Repo **Settings → Pages → Build and deployment → Deploy from a branch → `main` / root**.
3. Your app will be at `https://<username>.github.io/<repo>/`.
4. Back in Firebase: **Authentication → Settings → Authorized domains → Add domain** → `<username>.github.io`.

### 3. Phone / tablet

Open the site in Safari → Share → **Add to Home Screen**. Sign in once with Google; data syncs between devices.

## Writing texts

One sentence per line. Optional speaker before a colon, optional translation after a `|`:

```
A: 你好，请问苹果多少钱一斤？ | Hello, how much is a jin of apples?
B: 八块钱一斤。 | Eight kuai a jin.
```

## Data model (Firestore)

```
hanzi/{uid}/lessons/{id}    date, num, title, titleEn, notes
hanzi/{uid}/vocab/{id}      hanzi, py ("chao1 shi4"), meaning, pos, mw, example, exampleEn, lesson, srs
hanzi/{uid}/texts/{id}      title, titleEn, body, lesson, saved[]
hanzi/{uid}/grammar/{id}    title, pattern, explain, examples, mistake, lesson, srs
hanzi/{uid}/exercises/{id}  question, mine, answer, correct, note, resolved, lesson
hanzi/{uid}/meta/main       days[] (streak), questions[]
```

Pinyin is stored with tone numbers and converted to tone marks for display. Pinyin generation uses [pinyin-pro](https://github.com/zh-lib/pinyin-pro); speech uses the browser's built-in Chinese voice.
