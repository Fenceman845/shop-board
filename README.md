# Yaboo Shop Board

One web page for the PVC Shop, Welding Shop, Wood Shop, Yard and Mechanic Shop. Everyone signs in with their own email and password; what they see and what they can change depends on their role and the shops on their entry in the **People** list.

- `index.html` — the whole board. The page is hosted on GitHub Pages.
- `firestore.rules` — the security rules to paste into Firebase. They enforce the roles server-side.
- Data lives in Firebase (Firestore). Free tier.

## Roles

| Role | Sees | Can change |
|---|---|---|
| **Administrator** | every shop | everything, plus the People list |
| **Office** | every shop | everything: jobs, crews, equipment, yard, attachments, board settings |
| **Crew** | only the shops on their entry | posts progress on jobs (+ Progress); nothing else |
| **View only** | only the shops on their entry (or all) | nothing — use this for the TV and for shop screens |

The account in `ADMIN_EMAIL` at the top of `index.html` is always an administrator, even if the People list is empty, so the board can't lock itself out.

## Setup (about 15 minutes, all in a browser)

### 1. Firebase project (holds the data)

1. Go to https://console.firebase.google.com and click **Add project**. Name it `yaboo-shop-board`. Turn Google Analytics **off**. Create.
2. Left menu → **Databases & Storage → Firestore Database → Create database**. Pick a US location (`nam5` or `us-east4`). Start in **production mode**. Create.
3. On the Firestore page open the **Rules** tab. Replace everything with the contents of `firestore.rules` from this repo. Click **Publish**.
4. Left menu → **Security → Authentication → Get started**. Under **Sign-in method**, pick **Email/Password**, switch **Enable** on, Save.
5. Authentication → **Users** tab → **Add user**. Email: `joejr@yaboofence.com` (the `ADMIN_EMAIL`). Password: your own. Add. Everyone else gets added from the board itself (see People below), not from this screen.
6. Authentication → **Settings** tab → **Authorized domains** → **Add domain** → the GitHub Pages domain from step 3 below (`fenceman845.github.io`). Add your custom domain here too if you use one.
7. Gear icon (top left) → **Project settings** → scroll to **Your apps** → click the **`</>`** (Web) icon. Nickname `Shop Board`, leave Firebase Hosting unchecked, Register. Copy the `firebaseConfig = { ... }` block it shows.

### 2. Put the config in the page

Already done in this repo. If the Firebase project is ever recreated, replace the `FIREBASE_CONFIG` values near the top of the `<script type="module">` block in `index.html`. If the administrator's email ever changes, change `ADMIN_EMAIL` there **and** the same address in `firestore.rules`, then re-publish the rules.

### 3. GitHub Pages (hosts the page)

1. Push this folder to a GitHub repo (public on a free account — nothing in it is sensitive; the Firebase config is not a secret, the rules are what lock the data down).
2. Repo → **Settings → Pages** → Source: **Deploy from a branch** → Branch `main`, folder `/ (root)` → Save.
3. In a minute or two the page shows the URL, `https://fenceman845.github.io/shop-board/`. Open it.

Optional: **Custom domain** on that same Pages screen (e.g. `board.yaboofence.com`), then add a CNAME at your DNS host pointing to `fenceman845.github.io`. Remember to add the custom domain under Firebase Authorized domains too.

## People (who can sign in)

Sign in as the administrator → tap your name in the top bar (or Board settings) → **People**.

- **+ Add person**: name, email, role, starting password, and the shops they should see (Office and Administrator always see everything). This creates their login right there; no trip to Firebase. Tell them the email and password; they can change the password under their own name once signed in.
- Tap a person to change their role or shops. The change reaches their screen within a few seconds, even if they're signed in.
- **Remove access** takes them off the board. Their login still exists in Firebase → Authentication → Users; delete it there if you want it gone for good.
- The email doesn't have to be a real mailbox (`tv@yaboofence.com` is fine for the office TV). A real one lets that person use **Forgot password** on the sign-in screen.

Suggested starting list: yourself (Administrator, made automatically the first time you sign in), each office person (Office), one **View only / all shops** login for the office TV, one **View only / Welding Shop** login for a welding-shop screen, and each foreman as **Crew** with their shop.

## Using it

- **Sign in** on every device once, including the TV. Each device stays signed in.
- **TV:** sign in with the TV's view-only login, then tap **TV mode**. Full screen, screen stays awake, and it rotates through the shops that login can see. Seconds per screen is under Board settings.
- **Theme:** the Theme button cycles Auto / Dark / Light per device. Dark is meant for the TV.
- **Office and Administrator** tap **Edit** to switch editing on and off: add/move/finish jobs, crews, equipment, yard items, attachments, board settings. Every change carries the name from the People list.
- **Progress bars.** When you create a job, give it a **Quantity to make** and pick **pieces** or **linear feet**. The job then shows a bar and a running total on every screen, including the TV. Crews tap **+ Progress** and enter what they did (added to the total) or, to correct a mistake, the new total. Each entry is logged with the name, time, and an optional note. Office can also fix the total in Edit job. The bar turns green at 100%; marking the job Done is still an office action.
- **Jobs** move Coming up → Working now → Finished (or On hold). Finished jobs sit under "Finished · last 7 days" per shop; **Clear finished** deletes them.
- **Example data:** Board settings → Example data → Load / Remove. Everything it adds is marked EXAMPLE.
- **Attachments:** in Edit mode every job has an **Attach** button. Pick a PDF (a scanned worksheet) or a photo; on a phone the same button offers the camera. Attached files show as chips on the job for everyone who can see that shop; tap one to view it in place or open it in a new tab. Attachments are deleted with their job (Delete, or Clear finished).
  - Files are stored in the Firestore database itself, split into pieces, so there's no separate file service and no billing account. Limit is 12 MB per file. Scan at **150 dpi, black-and-white or grayscale, letter size** — that's roughly 50–200 KB per page and loads instantly on a phone. Photos are shrunk to about 2200 px on the long side before upload.
  - The free plan holds 1 GB total. At typical scan sizes that's several thousand pages; clearing finished jobs frees the space their attachments used.

## Passwords

- Anyone can change their own: tap their name in the top bar → new password.
- Forgot it: **Forgot password** on the sign-in screen emails a reset link (real email addresses only).
- For a login with a made-up email: Firebase → Authentication → Users → delete that user, then add the person again from People with a new starting password.

## Limits (free tier)

Firestore's free plan allows about 50,000 reads and 20,000 writes per day. A board with a TV and a handful of phones open all day uses a small fraction of that. If a toast ever says the daily limit was hit, it resets at midnight Pacific.
