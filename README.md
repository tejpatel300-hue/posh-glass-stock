# Posh Glass Stock

A single-file inventory & ordering app for Posh Glass — store staff place orders,
warehouse staff pick them and manage stock. Syncs live across every phone that
opens the page, using Firebase (free tier) as the shared database.

## 1. Create a free Firebase project

1. Go to https://console.firebase.google.com and sign in with any Google account.
2. Click **Add project** → give it a name (e.g. `posh-glass-stock`) → skip Google
   Analytics (not needed) → **Create project**.
3. In the left sidebar, click **Build → Firestore Database** → **Create database**
   → choose a location close to you → start in **production mode** → **Enable**.
4. Go to **Firestore → Rules** and replace the contents with:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /products/{doc} { allow read, write: if true; }
       match /orders/{doc}   { allow read, write: if true; }
     }
   }
   ```

   Click **Publish**. (This keeps things simple — anyone with the page link can
   read/write. That matches how the app worked before; see the "About security"
   note below if you want to lock it down later.)

5. Back in **Project Overview**, click the **</>** (web) icon to register a web
   app. Give it any nickname, skip hosting, click **Register app**. Firebase
   will show a `firebaseConfig` object — copy those values.

## 2. Add your config to `index.html`

Open `index.html` in this repo and find this block near the top:

```js
window.FIREBASE_CONFIG = {
  apiKey: "PASTE_YOUR_API_KEY",
  authDomain: "PASTE_YOUR_PROJECT.firebaseapp.com",
  projectId: "PASTE_YOUR_PROJECT_ID",
  storageBucket: "PASTE_YOUR_PROJECT.appspot.com",
  messagingSenderId: "PASTE_YOUR_SENDER_ID",
  appId: "PASTE_YOUR_APP_ID",
};
```

Replace each value with what Firebase gave you in step 1.5, then save.

## 3. Put it on GitHub

1. Go to https://github.com/new, create a new repository (public or private —
   either works with GitHub Pages), e.g. `posh-glass-stock`.
2. Upload `index.html` (and this `README.md`) — either drag-and-drop them on
   the repo's GitHub page ("uploading an existing file"), or via git:

   ```
   git init
   git add index.html README.md
   git commit -m "Posh Glass Stock app"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/posh-glass-stock.git
   git push -u origin main
   ```

## 4. Turn on GitHub Pages

1. In the repo, go to **Settings → Pages**.
2. Under **Build and deployment**, set **Source** to `Deploy from a branch`.
3. Set **Branch** to `main` and folder to `/ (root)` → **Save**.
4. Wait about a minute, then refresh the page — GitHub will show your live URL,
   something like `https://YOUR-USERNAME.github.io/posh-glass-stock/`.

That URL is what you share with your store and warehouse staff. Everyone opens
the **same link** on their own phone.

## 5. First load seeds the catalog

The first time anyone opens the page, it checks whether Firestore has any
products yet — if not, it loads the full starter catalog (all bongs, bowls,
and bubblers already priced and photographed) into Firestore automatically.
After that, every device just reads and writes that same shared data.

## What syncs, and how fast

- Quantities, prices, photos, new products, and orders all live in Firestore.
- Every open phone has a **live connection** (you'll see a "● Live" dot in the
  top right) — changes appear within a second or two on every other device,
  no refreshing needed.

## About security

The Firestore rules above allow anyone who has your config values (visible in
the page source) to read and write the data — there's no login check beyond
the name/role picker in the app, same as before. For an internal team tool
this is usually fine. If you want real protection later (so only your team can
write data), the next step up is Firebase Authentication — happy to help wire
that in when you're ready.

## Costs

Firebase's free "Spark" plan includes 1 GiB of storage and 50,000 reads /
20,000 writes per day — this app's entire catalog with photos is under 2 MB,
so you're nowhere close to the free limits for normal store/warehouse use.
