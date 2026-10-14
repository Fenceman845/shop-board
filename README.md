# Yaboo Shop Board

One web page for the PVC Shop, Welding Shop, Wood Shop, Yard and Mechanic Shop, plus a separate **Field** tab for projects out in the field. Everyone signs in with their own email and password; what they see and what they can change depends on their role and the shops on their entry in the **People** list.

- `index.html` — the whole board. The page is hosted on GitHub Pages.
- `firestore.rules` — the security rules to paste into Firebase. They enforce the roles server-side.
- Data lives in Firebase (Firestore). Free tier.

## Roles

| Role | Sees | Can change |
|---|---|---|
| **Administrator** | every shop, and the Field tab | everything, plus the People list |
| **Office** | every shop, and the Field tab | everything: jobs, crews, equipment, yard, attachments, board settings, field projects |
| **Crew** | only the shops on their entry | posts progress on jobs (+ Progress); nothing else |
| **View only** | only the shops on their entry (or all) | nothing — use this for the TV and for shop screens |

The Field tab is office and administrator only. Crew and view-only logins don't get the tab and the rules refuse them the data.

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
- **Example data:** Board settings → Example data → Load / Remove. Everything it adds is marked EXAMPLE. The Field tab has its own **Load example projects** button while it is empty; Remove clears both.
- **Attachments:** in Edit mode every job has an **Attach** button. Pick a PDF (a scanned worksheet) or a photo; on a phone the same button offers the camera. Attached files show as chips on the job for everyone who can see that shop; tap one to view it in place or open it in a new tab. Attachments are deleted with their job (Delete, or Clear finished).
  - Files are stored in the Firestore database itself, split into pieces, so there's no separate file service and no billing account. Limit is 12 MB per file. Scan at **150 dpi, black-and-white or grayscale, letter size** — that's roughly 50–200 KB per page and loads instantly on a phone. Photos are shrunk to about 2200 px on the long side before upload.
  - The free plan holds 1 GB total. At typical scan sizes that's several thousand pages; clearing finished jobs frees the space their attachments used.

## Field tab

A separate tab at the end of the tab rail for projects out in the field. Office and administrator logins only.

- **Job board.** One card per project, grouped by where it is in its life: Awarded → Paperwork & submittals → Material & fabrication → Ready to schedule → Installing → Punch list → Closed out. On a wide screen the stages sit side by side; on a phone they stack with on-site work first. The chips above the board filter to one stage (Closed out lives there), and a project-manager filter appears once more than one PM has projects.
- **Project page.** Tap a card. Laid out in the order of a foreman's job packet: stage (tap a stage in Edit to move it), **labor type** (open shop / prevailing wage / union) and **dig ticket** chips, customer / GC, location (opens in maps), job #, PM, foreman / crew, dates, a "next step / waiting on" line, and then:
  - **Contacts** — name, role, phone (tap to call). GC super, GC PM, site contact, inspector.
  - **Scope of work** — plain text, for jobs where we only install part of the project. Edit project → Scope of work.
  - **Portions of the project** — each piece of the job with its own quantity (LF or each), optional bid item #, status and progress bar. **+ Progress** logs what was installed, the same way the shops post progress. No dollar amounts anywhere on the board.
  - **In the shop** — the shop jobs tied to this project, with their progress bars. On a shop tab, Edit a job and set **For** to the project.
  - **Paperwork** — submittals, shop drawings, JHA, insurance, permits, each with a status and date; the dig ticket with its number, date called, clear / not clear, and expiry. **Edit paperwork** (or tap the dig chip).
  - **Drawings & paperwork** — attachments in three groups: approved shop drawings, contract / layout drawings, other. Same limits as shop attachments.
  - **Crew on site** — the days crews are scheduled on the job, and the crew-days already spent.
  - **Site notes** — hours, access, parking, safety requirements. Edit project → Site notes.
  - **Log** — dated entries with the name on them (**+ Log entry**). Replaces a single notes box.
- **Print cover sheet** (button at the top of a project page) prints a one-page letter-size sheet to hand to the crew: address, foreman, labor type, dig ticket, dates, contacts, scope, portions and quantities, site notes, what is in the packet, and lines for notes from the field. It never carries the job # or any pricing. On a phone, the share / print menu does the same.
- **Shop jobs and the Field tab.** Every shop job has a **For** setting: project work (tied to a field project, or not), **over the counter**, or **residential**. Counter orders get their own card on the shop tab and their own block on the All shops overview, so they don't mix in with project work.
- **Crew schedule.** The other view on the Field tab: field crews down the side, the week across the top. In Edit, **+ Assign** on a cell picks the job(s) for that crew and day, with an optional note (shop day, off, rain) and "through Friday" to fill the week. On a phone it shows one day at a time.
- **Field crews** are their own list (+ Field crew), separate from the shop crews.
- Each project page has its own link (`#field/<id>`), so it can be texted or bookmarked.
- TV mode rotates through the shop screens only. A screen that is on the Field tab when TV mode starts stays on it.

The field data lives in its own collections (`fieldJobs`, `fieldPortions`, `fieldCrews`, `fieldFiles`). **Whenever `firestore.rules` changes in this repo, it has to be pasted into Firebase → Firestore Database → Rules and published again** — the page and the rules are deployed separately. Until the rules that cover those collections are published, the Field tab shows a note saying so and the shop tabs carry on as before.

## Passwords

- Anyone can change their own: tap their name in the top bar → new password.
- Forgot it: **Forgot password** on the sign-in screen emails a reset link (real email addresses only).
- For a login with a made-up email: Firebase → Authentication → Users → delete that user, then add the person again from People with a new starting password.

## Limits (free tier)

Firestore's free plan allows about 50,000 reads and 20,000 writes per day. A board with a TV and a handful of phones open all day uses a small fraction of that. If a toast ever says the daily limit was hit, it resets at midnight Pacific.
