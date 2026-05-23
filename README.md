# 📚 BookFinder — Setup & Deployment Guide

A book search and reading list app built with OpenLibrary + Firebase Firestore, hosted on GitHub Pages.

---

## Step 1 — Create a Firebase Project

1. Go to **https://console.firebase.google.com**
2. Click **"Add project"** → name it (e.g. `book-finder`) → click through the setup wizard
3. Once inside the project, click **"</> Web"** (the web icon) to add a web app
4. Give it a nickname (e.g. `book-finder-web`) → click **"Register app"**
5. Copy the `firebaseConfig` object shown — it looks like this:

```js
const firebaseConfig = {
  apiKey:            "AIza...",
  authDomain:        "your-project.firebaseapp.com",
  projectId:         "your-project-id",
  storageBucket:     "your-project.appspot.com",
  messagingSenderId: "1234567890",
  appId:             "1:123...:web:abc..."
};
```

---

## Step 2 — Enable Firestore

1. In the Firebase Console, click **"Firestore Database"** in the left sidebar
2. Click **"Create database"**
3. Choose **"Start in test mode"** *(allows all reads/writes for 30 days — fine for personal use)*
4. Select a location close to you → click **"Enable"**

---

## Step 3 — Paste Your Config into index.html

Open `index.html` and find this block near the bottom:

```js
const FIREBASE_CONFIG = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT.firebaseapp.com",
  ...
};
```

Replace the placeholder values with your actual config from Step 1.

---

## Step 4 — Create a GitHub Repository

1. Go to **https://github.com/new**
2. Name the repo (e.g. `book-finder`) — keep it **Public**
3. **Don't** initialise with a README (you'll push your files)
4. Click **"Create repository"**

---

## Step 5 — Push Your Files

In your terminal, from inside the `book-finder` folder:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/book-finder.git
git push -u origin main
```

---

## Step 6 — Enable GitHub Pages

1. In your GitHub repo, go to **Settings → Pages**
2. Under **"Source"**, choose **"Deploy from a branch"**
3. Select **branch: main** and **folder: / (root)** → click **Save**
4. Wait ~60 seconds, then visit:

```
https://YOUR_USERNAME.github.io/book-finder/
```

🎉 Your app is live!

---

## Optional — Lock Down Firestore Security

The "test mode" rules expire after 30 days. To make them permanent for personal use, go to **Firestore → Rules** and replace the content with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

> For a production/shared app, use Firebase Authentication and scope rules per user.

---

## How the App Works

| Feature | How |
|---|---|
| Book search | OpenLibrary `/search.json` API (free, no key needed) |
| Cover images | OpenLibrary cover CDN |
| Save books | Firestore `users/default-user/reading-list` |
| Real-time sync | Firestore `onSnapshot` listener |
| Status toggle | `updateDoc` on the Firestore document |
| Remove book | `deleteDoc` on the Firestore document |
| Persistence | Firestore — survives page refresh, browser close, device switch |
