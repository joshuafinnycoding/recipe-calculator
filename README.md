# Mise en Place — GitHub Pages Setup Guide

A click-through guide to deploy the recipe calculator with a Google Sheet as the database, hosted free on GitHub Pages, and secured with a secret token so only you can use it.

Roughly **15–20 minutes** start to finish. Three phases:

1. **Set up the Google Sheet + Apps Script** (with a secret token) — 10 minutes
2. **Deploy the HTML to GitHub Pages** — 5 minutes
3. **Connect from your phone** — 1 minute

You'll need: a Google account, a GitHub account (free), and a phone or browser.

---

## Phase 1 — Set up the Google Sheet backend

### 1.1 Generate your secret token

Before touching anything else, you need a long random string that acts as the password between your app and your Sheet. Open any browser, then:

1. Press **F12** (Windows/Linux) or **Cmd+Option+I** (Mac) to open DevTools.
2. Click the **Console** tab.
3. Paste this and press Enter:
   ```
   crypto.randomUUID()
   ```
4. You'll see something like `"550e8400-e29b-41d4-a716-446655440000"`. Copy this value (without the quotes).
5. **Save it somewhere safe** — a password manager, a notes app, anywhere you can find it again. You'll paste this exact value into two places.

If you can't access DevTools, just type a random 32+ character string. It needs to be hard to guess.

### 1.2 Create the Google Sheet

Go to **[sheets.new](https://sheets.new)**. This creates a fresh blank Google Sheet.

Rename it from "Untitled spreadsheet" to **"Recipe Calculator DB"** (click the title in the top-left).

### 1.3 Open the Apps Script editor

In the sheet, click the menu: **Extensions → Apps Script**.

A new tab opens with a code editor showing a default `myFunction()`. Select all the code and delete it.

### 1.4 Paste the backend code and add your token

Open the file **`Code.gs`** (came alongside this guide). Copy its entire contents and paste them into the Apps Script editor.

**Important:** find this line near the top of the code:
```
var TOKEN = 'PASTE-YOUR-TOKEN-HERE';
```

Replace `PASTE-YOUR-TOKEN-HERE` with the token you generated in Step 1.1. Keep the quotes:
```
var TOKEN = '550e8400-e29b-41d4-a716-446655440000';
```

Click the **save icon** (💾) at the top, or press **Ctrl+S** / **Cmd+S**. Give the project a name when prompted — "Recipe Calculator" works fine.

### 1.5 Run the one-time setup

In the toolbar at the top of the Apps Script editor, click the function dropdown (it might say "doGet" or "doPost") and choose **`initSheets`**. Then click the **Run** button (▶).

The first time you run it, Google asks for permission:

1. A dialog says "Authorization required" → click **Review permissions**.
2. Pick your Google account.
3. You'll see a "Google hasn't verified this app" screen. This is expected — you wrote the script yourself. Click **Advanced** → **Go to Recipe Calculator (unsafe)**.
4. Click **Allow** on the permissions screen.

The script runs. Switch back to your Sheet tab — you'll see two new tabs at the bottom: **`ingredients`** and **`recipes`**, with header rows already in place.

### 1.6 Deploy as a Web App

Back in the Apps Script editor, click **Deploy** (top right) → **New deployment**.

- Click the gear icon (⚙) next to "Select type" → choose **Web app**
- Fill in:
  - **Description:** `Recipe API v1`
  - **Execute as:** **Me (your email)**
  - **Who has access:** **Anyone**
- Click **Deploy**.

> "Anyone" sounds scary but the token gate protects you. Without the token, the URL alone returns nothing useful.

Google may ask you to authorize again — same flow as before.

When it's done, you'll see a **Web app URL** like:
```
https://script.google.com/macros/s/AKfycby.../exec
```

**Click "Copy"** and save this URL alongside your token. You'll need both for Phase 3.

---

## Phase 2 — Deploy the HTML to GitHub Pages

### 2.1 Create a GitHub account (if you don't have one)

Go to **[github.com/signup](https://github.com/signup)** and create a free account. No credit card needed.

### 2.2 Create a new public repository

Once signed in:

1. Click the **+** icon in the top-right → **New repository**.
2. **Repository name:** anything you like — `recipe-calculator` or `kitchen-ledger` works.
3. Visibility: **Public** (required for free GitHub Pages).
4. **Check** the box that says "Add a README file" (so the repo isn't empty).
5. Click **Create repository**.

> The repo being public sounds concerning, but the HTML file you'll upload contains **no secrets**. The Apps Script URL and token are only entered by you on your phone after the site is live — they're never in the source code.

### 2.3 Upload the HTML file

On your new repo's main page:

1. Click **Add file** → **Upload files**.
2. Drag the **`index.html`** file (from the package alongside this guide) onto the upload zone.
   - **The filename must be exactly `index.html`** — that's how GitHub Pages serves it as the homepage. If your file is named something else (like `recipe-calculator.html`), rename it before uploading.
3. Scroll down. Under "Commit changes," leave the default message and click **Commit changes**.

### 2.4 Enable GitHub Pages

Still in your repo:

1. Click the **Settings** tab (top of the page).
2. In the left sidebar, click **Pages**.
3. Under "Build and deployment" → "Source," pick **Deploy from a branch**.
4. Under "Branch," select **main** (and folder **/(root)**), then click **Save**.

GitHub will start building your site. Wait 1–2 minutes, then refresh the page. You'll see a green banner:

```
Your site is live at https://your-username.github.io/recipe-calculator/
```

**Copy that URL.** That's your app.

---

## Phase 3 — Connect from your phone

### 3.1 Open the URL on your phone

In Safari (iPhone) or Chrome (Android), open the GitHub Pages URL from Step 2.4. You'll see a **"One-time setup"** screen with two fields.

### 3.2 Paste your URL and token

- In **Apps Script Web App URL**, paste the Web app URL from Step 1.6.
- In **Secret token**, paste the token from Step 1.1.

Tap **Connect**.

The app fetches your (currently empty) sheet, shows the main screen, and is ready to use. Add an ingredient and watch the **`ingredients`** tab in your Google Sheet fill in.

### 3.3 (Recommended) Add to Home Screen

To make it feel like an app:

**iPhone (Safari):**
1. Tap the share icon (square with up-arrow) at the bottom.
2. Scroll down → **Add to Home Screen**.
3. Tap **Add**. Now there's a "Mise en Place" icon on your home screen that opens the app full-screen with no browser chrome.

**Android (Chrome):**
1. Tap the three-dot menu (top-right).
2. Tap **Add to Home screen**.
3. Tap **Add**.

---

## Daily Usage

- **The Sheet is the source of truth.** Open your Google Sheet directly any time to view, back up (File → Make a copy), or edit by hand. The app reads from and writes to it.
- The **green sync dot** at the top right means everything's saved. It turns **yellow** while saving and **red** if a save fails (then retries automatically).
- The app caches data locally too, so it loads instantly and works **offline** — changes sync on the next request when you're back online.
- **Multiple devices** — open the same GitHub Pages URL on each device. On a new device, you'll see the setup screen again and need to paste the URL + token once. Same data appears everywhere.

---

## Updating the app

**To change the HTML** (e.g. tweak the UI, add features later):

1. In your repo, click the **`index.html`** file.
2. Click the pencil icon (✏️) to edit, or upload a new file replacing it.
3. Commit. GitHub Pages redeploys automatically in 1–2 minutes.

**To change the Apps Script code:**
- In the Apps Script editor, edit the code.
- **Deploy → Manage deployments → pencil icon → set Version to "New version" → Deploy.**
- This keeps the same URL, so the app continues working without changes on your phone.

> If you ever pick "New deployment" (instead of editing the existing one), you'll get a new URL and have to update it in the app via the ⚙ button.

**To rotate your token** (if you ever suspect it leaked):

1. Generate a new token using the `crypto.randomUUID()` trick.
2. Update the `TOKEN` constant in the Apps Script code.
3. Save and re-deploy a new version.
4. On each device, tap the ⚙ icon in the app and paste the new token. The URL stays the same.

---

## Free tier limits

- **GitHub Pages:** Free with a 1 GB site size limit and 100 GB/month soft bandwidth limit. Your app uses ~50 KB, so this is a non-concern.
- **Google Apps Script:** 20,000 URL fetch calls per day for consumer Gmail accounts. You'd have to be very busy in the kitchen to hit that.
- **Google Sheets:** 10 million cells per spreadsheet. You'll never get close.

---

## Troubleshooting

**"Could not connect" when pasting the URL**
The URL must end with `/exec` (not `/dev`). If you only see `/dev`, you used "Test deployments" instead of "New deployment" — go back to **Deploy → New deployment**.

**"Token check failed"**
The token you pasted into the app doesn't match the one in your Apps Script. Tap ⚙ in the app, re-paste the correct token. Or check your Apps Script code — make sure `var TOKEN = '...'` has your token between the quotes, not the placeholder text.

**Sync shows "save failed" repeatedly**
Open your Apps Script project → **Executions** in the left sidebar. The latest run will show the error. Common cause: you re-deployed and made a new deployment (new URL). Open the app's ⚙ and update the URL.

**The Sheet has unexpected data**
The app does a "wipe and rewrite" of the rows below the header on every save. **Don't add columns** to the `ingredients` or `recipes` sheets — but you can edit cells in existing columns safely (refresh the app afterwards). To back things up, use **File → Make a copy** in the Sheet.

**Site shows a 404 on GitHub Pages**
The file must be named `index.html` exactly. Also, GitHub Pages can take 1–2 minutes after the first commit to build. Wait a bit and refresh.

**iPhone keeps zooming when I tap an input**
This shouldn't happen — inputs are sized to prevent iOS auto-zoom. If it does, double-tap with two fingers to zoom out, then it'll stay calm.

---

That's it. The setup is one-time; from now on you just tap the home-screen icon and cook.
