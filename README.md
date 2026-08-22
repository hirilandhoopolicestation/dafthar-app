# Bidheyseenge Dafthar — Expatriate Registry (with login + admin approval)

This version stores data on a real server (Google Firebase) and requires:
- Every user to log in with email + password
- New sign-ups to sit in "pending" until **you**, the administrator, approve them
- Only approved users can see or edit the registry

Nothing to code — just fill in some settings on Firebase's free website. Takes about 15 minutes the first time.

## Files in this folder
- `index.html` — the whole app (login, pending screen, admin panel, registry)
- `firebase-config.js` — **you fill this in** with your project's keys (Step 2)
- `firestore.rules` — security rules you paste into Firebase (Step 4)
- `manifest.json`, `sw.js`, icons — makes it installable on iPhone

---

## Step 1: Create your Firebase project

1. Go to https://console.firebase.google.com and sign in with any Google account.
2. Click **Add project**. Give it a name (e.g. `dafthar-registry`). You can turn off Google Analytics — not needed. Click **Create project**.

## Step 2: Register a web app and get your config keys

1. In your new project, click the **</>** (web) icon on the project overview page.
2. Give the app a nickname (e.g. `dafthar-web`). You do **not** need Firebase Hosting checked here (we'll cover hosting separately). Click **Register app**.
3. Firebase shows you a code block with a `firebaseConfig = { ... }` object. Copy the values (`apiKey`, `authDomain`, `projectId`, etc.).
4. Open `firebase-config.js` in this folder and paste your real values in, replacing the `PASTE_...` placeholders. Save the file.

## Step 3: Turn on Email/Password login

1. In the left sidebar of the Firebase Console, go to **Build → Authentication**.
2. Click **Get started**.
3. Click **Email/Password**, toggle it **Enabled**, click **Save**.

## Step 4: Create the database and paste in the security rules

1. In the left sidebar, go to **Build → Firestore Database**.
2. Click **Create database**. Choose a location close to you, and start in **Production mode**. Click **Create**.
3. Once created, click the **Rules** tab at the top.
4. Delete everything in the box, then open `firestore.rules` from this folder, copy its entire contents, and paste it into the box.
5. Click **Publish**.

This is what enforces: nobody can read/write the registry unless an admin has approved them, and nobody can make themselves an admin.

## Step 5: Host the files

You need these files served over **https**. Easiest free option: **GitHub Pages**.

1. Go to https://github.com and create a free account if needed.
2. Click **New repository**, name it anything (e.g. `dafthar-app`), set it **Public**, click **Create repository**.
3. Click **Add file → Upload files**, drag in *every file in this folder* (`index.html`, `firebase-config.js`, `firestore.rules`, `manifest.json`, `sw.js`, and the 3 icon `.png` files). Click **Commit changes**.
4. Go to **Settings → Pages**. Under "Build and deployment": Source = **Deploy from a branch**, Branch = `main`, folder = `/ (root)`. Click **Save**.
5. Wait 1–2 minutes, refresh — you'll get a live link like `https://yourusername.github.io/dafthar-app/`.

**Alternative:** drag the folder onto https://app.netlify.com/drop for an instant link (no account needed) — works the same way.

## Step 6: Authorize your hosting domain in Firebase

1. Back in Firebase Console → **Authentication → Settings → Authorized domains**.
2. Click **Add domain**, paste in your GitHub Pages (or Netlify) domain, e.g. `yourusername.github.io`. Save.

Without this step, login will fail with a "domain not authorized" error.

## Step 7: Create yourself as the first admin

The very first account always signs up as a normal "pending" user — there's no admin yet, so you make yourself one manually, once:

1. Open your live link, tap **Request access**, sign up with your own name/email/password.
2. In Firebase Console, go to **Build → Firestore Database → Data**.
3. Open the `users` collection, click on the document with your account (matches your email).
4. Edit two fields: change `status` from `pending` to `approved`, and `role` from `user` to `admin`. Save.
5. Reload the app on your phone (or just wait a few seconds — it updates live) — you'll now see the **Administrator** view with an **Approvals** tab and a **Registry** tab.

From now on, anyone else who signs up will show up under your **Approvals** tab, where you tap **Approve** or **Reject**. You never need to touch the Firebase Console again for day-to-day use.

## Step 8: Add it to your iPhone Home Screen

1. Open your live link in **Safari** (must be Safari, not Chrome).
2. Tap the **Share** icon → scroll down → **Add to Home Screen** → **Add**.
3. You'll now have a "Dafthar" app icon that opens full-screen.

---

## Using the app

**Everyone:**
- Sign in with email + password. New requests wait on a "Waiting for approval" screen that updates itself automatically once approved — no refreshing needed.

**Approved users:**
- Add / edit / delete registry entries (Full Name, Country, Passport Number).
- Search instantly across all fields.
- Export the whole registry to CSV any time (opens in Excel/Numbers/Sheets).
- Data is shared live — if two people are approved and both open the app, they see the same up-to-date list.

**You (the admin):**
- **Approvals** tab: see and approve/reject anyone who's signed up.
- **All users** tab: see everyone's status; revoke access from someone previously approved.
- **Registry** tab: same add/edit/delete/search/export tools as everyone else.

## Cost

Firebase's free tier is generous (tens of thousands of reads/writes per day) — for a small registry like this, you will not be charged anything under normal use.

## Data & privacy notes

- Data lives in your own private Firebase project — only you control it, and only approved users can read/write it (enforced by the rules in `firestore.rules`, not just by the app's interface).
- If you ever need to remove someone's access, use the **All users** tab — no need to touch Firebase Console.
- Keep a CSV export as an occasional backup if this data matters long-term.
