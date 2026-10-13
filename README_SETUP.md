# Bluebird Money Manager — Google sign-in + cloud sync setup

This package preserves the pastel-blue app and adds a Firebase integration scaffold. Cloud sync is **not active until you create/configure your own Firebase project**. The Firebase web config is public client configuration; never paste service-account keys or service-role secrets into this app.

## 1) Create a Firebase project (free tier)
1. Open https://console.firebase.google.com/ and create a project.
2. Add a Web app (`</>`). Copy its web config values.
3. The supplied `index.html` is already configured with the Firebase web-app configuration shown in your Firebase console. These browser configuration values are not service-account secrets. Do not add service-account keys or private server credentials.
4. Firebase Console → Authentication → Get started → Sign-in method → enable **Google**.
5. Authentication → Settings → Authorized domains: add your deployed site hostname (for GitHub Pages this is typically `YOURNAME.github.io`; if using a project path, authorize the hostname, not the path).
6. Firestore Database → Create database. Choose production mode.
7. Firestore → Rules: paste the contents of `firestore.rules` and Publish.

The rules limit reads/writes to the signed-in user's own `/users/{uid}/bluebird/main` document. Do not replace these with public read/write rules.

## 2) Free static hosting with GitHub Pages
1. Create/sign into GitHub and make a new repository, e.g. `bluebird-money-manager`.
2. Upload `index.html`, `manifest.webmanifest`, `icon.svg`, and `sw.js` to the repository root. Keep `firestore.rules` and this README for reference (they need not be public on the website; preferably keep rules in a private repo or copy them into Firebase Console).
3. GitHub repository → Settings → Pages → Deploy from a branch → `main` → `/ (root)` → Save.
4. Wait for the Pages URL, open it in laptop Chrome, then sign in with Google. Use the same URL on Android Chrome.
5. On Android Chrome, menu ⋮ → **Add to Home screen** or **Install app**.

GitHub Pages serves the app over HTTPS, required for service workers and secure web app behavior. Firebase has separate free-tier quotas and terms; usage beyond the free allowance may require billing.

## 3) Data migration and important limits
- On the first Google sign-in, if the cloud document does not exist, this build copies the current browser's `bluebird_money_manager_v2` local data into that user's cloud document.
- If the cloud document already exists, cloud data is treated as canonical and replaces this device's local app data on reload. **Export a JSON backup from the old app before first sign-in**, and do not sign in on a device with important unsynced records unless you have backed them up. Merge of two independently edited device databases is not implemented.
- Current implementation syncs the app database as one per-user Firestore document. It is suitable for personal-scale data but has Firestore document-size limits; large databases should be migrated to per-record documents later.
- Do not use the app as the only copy of important financial records. Export periodic JSON backups.
- Google sign-in and sync require internet. Local mode remains available before configuration. This is a starter implementation, not an audited financial-data product.

## 4) Test checklist
- Sign in on laptop; add a test transaction; verify the cloud status says saved.
- Open the same URL on phone and sign in with the same Google account; confirm the transaction appears.
- Add a test savings contribution on phone; confirm it appears on laptop after refresh.
- Sign out and verify another Google account cannot read the first account's data.
- Export a JSON backup.

## Files
- `index.html` — Bluebird app with Firebase Google Auth and cloud sync scaffold
- `manifest.webmanifest`, `icon.svg`, `sw.js` — installable web app shell
- `firestore.rules` — per-user database access rules
