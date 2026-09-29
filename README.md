# Yaboo Shop Board

One web page for the PVC Shop, Welding Shop, Wood Shop, Yard and Mechanic Shop. Anyone with the link can view it (office TV, phones, no login). Changing it takes the editor password.

- `index.html` — the whole board. The page is hosted on GitHub Pages.
- `firestore.rules` — the security rules to paste into Firebase.
- Data lives in Firebase (Firestore). Free tier.

## Setup (about 15 minutes, all in a browser)

### 1. Firebase project (holds the data)

1. Go to https://console.firebase.google.com and click **Add project**. Name it `yaboo-shop-board`. Turn Google Analytics **off**. Create.
2. Left menu → **Databases & Storage → Firestore Database → Create database**. Pick a US location (`nam5` or `us-east4`). Start in **production mode**. Create.
3. On the Firestore page open the **Rules** tab. Replace everything with the contents of `firestore.rules` from this repo. Click **Publish**.
4. Left menu → **Security → Authentication → Get started**. Under **Sign-in method**, pick **Email/Password**, switch **Enable** on, Save.
5. Authentication → **Users** tab → **Add user**. Email: `joejr@yaboofence.com` (it has to match `EDITOR_EMAIL` in `index.html`). Password: the editor password everyone will use. Add.
6. Authentication → **Settings** tab → **Authorized domains** → **Add domain** → the GitHub Pages domain from step 2 below (`fenceman845.github.io`). Add your custom domain here too if you use one.
7. Gear icon (top left) → **Project settings** → scroll to **Your apps** → click the **`</>`** (Web) icon. Nickname `Shop Board`, leave Firebase Hosting unchecked, Register. Copy the `firebaseConfig = { ... }` block it shows.

### 2. Put the config in the page

Already done in this repo. If the Firebase project is ever recreated, replace the `FIREBASE_CONFIG` values near the top of the `<script type="module">` block in `index.html`, and keep `EDITOR_EMAIL` matching the editor user.

### 3. GitHub Pages (hosts the page)

1. Push this folder to a GitHub repo (public on a free account — nothing in it is sensitive; the Firebase config is not a secret, the rules are what lock the data down).
2. Repo → **Settings → Pages** → Source: **Deploy from a branch** → Branch `main`, folder `/ (root)` → Save.
3. In a minute or two the page shows the URL, `https://fenceman845.github.io/shop-board/`. Open it.

Optional: **Custom domain** on that same Pages screen (e.g. `board.yaboofence.com`), then add a CNAME at your DNS host pointing to `fenceman845.github.io`. Remember to add the custom domain under Firebase Authorized domains too.

## Using it

- **TV:** open the URL in the TV's browser (or a laptop/stick plugged into it) and tap **TV mode**. Full screen, screen stays awake, rotates All → PVC → Welding → Wood → Yard → Mechanic. Seconds per screen is under Board settings.
- **Theme:** the Theme button cycles Auto / Dark / Light per device. Dark is meant for the TV.
- **Editing:** tap **Edit**, enter your name and the editor password. The device stays signed in afterwards; tap Edit to switch editing on and off. Your name is stamped on every change you make. Sign out is under Board settings.
- **Jobs** move Coming up → Working now → Finished (or On hold). Finished jobs sit under "Finished · last 7 days" per shop; **Clear finished** deletes them.
- **Example data:** Board settings → Example data → Load / Remove. Everything it adds is marked EXAMPLE.

## Changing the editor password

Firebase console → Authentication → Users → the editor user → ⋮ → Reset password (or delete the user and add it again with the new password). Everyone signs in again with the new one.

## Limits (free tier)

Firestore's free plan allows about 50,000 reads and 20,000 writes per day. A board with a TV and a handful of phones open all day uses a small fraction of that. If a toast ever says the daily limit was hit, it resets at midnight Pacific.
